# E2E Testing Framework Patterns

Guidance on structuring end-to-end tests in four widely adopted frameworks.
Choose a framework based on application type, team language preferences,
browser coverage requirements, and CI integration needs.

---

## Playwright

**Best for**: Modern web applications requiring cross-browser coverage, combined
API and UI testing in a single suite, and built-in parallelism.

### Key patterns

- **Browser contexts for isolation** — Create a fresh browser context per test
  to avoid shared cookies, storage, or state leaking between scenarios.
- **API + UI in one test** — Use `request` fixtures to seed data via API before
  driving the UI, then assert final state through API or database queries.
- **Auto-waiting** — Playwright auto-waits for elements to be actionable. Avoid
  explicit sleeps; instead rely on locator assertions like
  `await expect(page.getByRole('button')).toBeEnabled()`.
- **Trace on failure** — Enable trace collection in CI so failures include a
  full timeline of actions, network requests, and DOM snapshots.

### Recommended project structure

```
tests/
  e2e/
    fixtures/           # shared test fixtures (auth state, test data factories)
    checkout/
      happy-path.spec.ts
      payment-failure.spec.ts
    registration/
      standard-signup.spec.ts
  playwright.config.ts  # browser matrix, base URL, timeouts
```

### Example — login then verify dashboard

```typescript
import { test, expect } from '@playwright/test';

test('user logs in and sees dashboard', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('user@example.com');
  await page.getByLabel('Password').fill('secureP@ss1');
  await page.getByRole('button', { name: 'Sign in' }).click();

  await expect(page).toHaveURL('/dashboard');
  await expect(page.getByRole('heading', { name: 'Welcome' })).toBeVisible();
});
```

### Tips

- Use `test.describe.configure({ mode: 'parallel' })` for independent scenarios.
- Store authenticated state with `storageState` to skip login in non-auth tests.
- Run `npx playwright test --project=chromium` locally for fast feedback; run
  the full browser matrix in CI.

---

## Cypress

**Best for**: Single-page applications, teams wanting fast feedback loops, and
projects that benefit from component plus e2e testing in one tool.

### Key patterns

- **Command chaining** — Cypress commands are enqueued and run in order.
  Chain assertions directly: `cy.get('[data-testid="total"]').should('contain', '$42.00')`.
- **Intercept for stubbing** — Use `cy.intercept()` to stub or spy on network
  requests. This avoids reliance on external services during e2e runs.
- **Custom commands** — Extract repeated sequences (login, add-to-cart) into
  custom commands registered in `cypress/support/commands.ts`.
- **Retry-ability** — Cypress automatically retries assertions until the timeout.
  Do not add `cy.wait(ms)` for DOM changes; let retry-ability handle it.

### Recommended project structure

```
cypress/
  e2e/
    checkout/
      happy-path.cy.ts
      coupon-application.cy.ts
    registration/
      standard-signup.cy.ts
  fixtures/              # static test data JSON files
  support/
    commands.ts          # custom commands
    e2e.ts               # global before/after hooks
  cypress.config.ts      # base URL, viewports, timeouts
```

### Example — add item to cart

```typescript
describe('Cart', () => {
  beforeEach(() => {
    cy.intercept('GET', '/api/products/*', { fixture: 'product.json' }).as('getProduct');
    cy.visit('/products/widget-a');
    cy.wait('@getProduct');
  });

  it('adds item and shows updated cart count', () => {
    cy.getByTestId('add-to-cart').click();
    cy.getByTestId('cart-badge').should('contain', '1');
  });
});
```

### Tips

- Use `cy.session()` to cache login state across specs.
- Avoid visiting external domains; Cypress has same-origin limitations.
- Run `cypress run --spec "cypress/e2e/checkout/**"` to target a single workflow.

---

## Selenium WebDriver

**Best for**: Multi-language teams, projects requiring broad browser support
(including older browsers), and organizations with existing Selenium
infrastructure.

### Key patterns

- **Page Object Model (POM)** — Encapsulate page interactions in dedicated
  classes. Each page object exposes methods for user actions and assertions,
  hiding locator details from test code.
- **Explicit waits** — Use `WebDriverWait` with expected conditions instead of
  `Thread.sleep()`. Example: `wait.until(EC.visibility_of_element_located(...))`.
- **Grid for parallelism** — Run tests across browsers and OS combinations using
  Selenium Grid or a cloud provider (BrowserStack, Sauce Labs).
- **Driver management** — Use WebDriverManager or similar tools to auto-download
  matching driver binaries. Pin browser versions in CI for reproducibility.

### Recommended project structure (Python)

```
tests/
  e2e/
    pages/
      login_page.py
      dashboard_page.py
      checkout_page.py
    conftest.py           # fixtures: driver setup/teardown, base URL
    test_checkout.py
    test_registration.py
  requirements.txt        # selenium, webdriver-manager, pytest
```

### Example — Page Object in Python

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


class LoginPage:
    URL = "/login"

    def __init__(self, driver):
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    def open(self):
        self.driver.get(self.driver.base_url + self.URL)
        return self

    def login(self, email: str, password: str):
        self.driver.find_element(By.CSS_SELECTOR, "[data-testid='email']").send_keys(email)
        self.driver.find_element(By.CSS_SELECTOR, "[data-testid='password']").send_keys(password)
        self.driver.find_element(By.CSS_SELECTOR, "[data-testid='submit']").click()
        self.wait.until(EC.url_contains("/dashboard"))
        return self
```

### Tips

- Keep driver lifecycle in fixtures/setup — never instantiate a driver inside
  a test method.
- Use `data-testid` selectors even in Selenium; they are more stable than XPath.
- Capture screenshots on failure for CI debugging.

---

## TestCafe

**Best for**: Teams that want quick setup with no WebDriver dependency, built-in
automatic waiting, and simple parallelism.

### Key patterns

- **No WebDriver** — TestCafe injects a proxy script into the page. Install with
  `npm install testcafe` and run immediately.
- **Selector + assertion model** — Use `Selector` to locate elements and built-in
  assertion methods that auto-wait: `await t.expect(Selector('#total').innerText).eql('$42.00')`.
- **Roles for authentication** — Define a `Role` that logs in once and reuse it
  across tests to skip repeated login.
- **Concurrency flag** — Run tests in parallel with `testcafe chrome -c 4`
  (four browser instances).

### Recommended project structure

```
tests/
  e2e/
    page-models/
      login-page.ts
      checkout-page.ts
    helpers/
      roles.ts            # authenticated roles
      data-factory.ts     # API-based test data creation
    checkout/
      happy-path.test.ts
    registration/
      standard-signup.test.ts
  .testcaferc.json        # browsers, base URL, screenshots
```

### Example — role-based authentication

```typescript
import { Role, Selector } from 'testcafe';

const regularUser = Role('https://app.example.com/login', async (t) => {
  await t
    .typeText('[data-testid="email"]', 'user@example.com')
    .typeText('[data-testid="password"]', 'secureP@ss1')
    .click('[data-testid="submit"]');
});

fixture('Dashboard').page('https://app.example.com');

test('authenticated user sees welcome banner', async (t) => {
  await t.useRole(regularUser);
  await t.expect(Selector('[data-testid="welcome-banner"]').exists).ok();
});
```

### Tips

- Use `RequestMock` to stub external API calls during tests.
- Enable screenshots and videos on failure in `.testcaferc.json`.
- Use `--skip-js-errors` cautiously — prefer fixing console errors over hiding them.

---

## Choosing a Framework — Decision Checklist

| Question | Playwright | Cypress | Selenium | TestCafe |
|---|---|---|---|---|
| Need cross-browser (Chrome, Firefox, Safari)? | Yes | Partial | Yes | Yes |
| Need mobile browser testing? | Yes (emulation) | No | Yes (Appium) | Yes (emulation) |
| Need combined API + UI tests? | Built-in | Plugin | Manual | Manual |
| Team language? | JS/TS, Python, Java, C# | JS/TS | Any | JS/TS |
| Need component testing too? | Experimental | Built-in | No | No |
| Need WebDriver compatibility? | No | No | Yes | No |
| Parallel execution built-in? | Yes | Via CI split | Grid | Yes |
