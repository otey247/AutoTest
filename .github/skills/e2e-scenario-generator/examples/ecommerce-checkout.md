# E2E Scenario Suite: E-Commerce Checkout

A complete set of end-to-end scenarios covering the checkout workflow of a
typical e-commerce application — from cart review through order confirmation.

---

## Systems Involved

| System | Role |
|---|---|
| Web UI | Storefront single-page application |
| API Gateway | Routes requests, enforces authentication |
| Cart Service | Manages cart state and pricing |
| Inventory Service | Checks and reserves stock |
| Payment Service | Integrates with payment processor |
| Order Service | Creates and persists orders |
| Notification Service | Sends order confirmation email |
| PostgreSQL | Primary data store |
| Redis | Session and cart cache |

---

## Shared Preconditions

All scenarios assume:

- A seeded product catalog with at least three in-stock items and one out-of-stock item.
- A registered test user with a saved shipping address and saved payment method.
- A guest user (no account) for guest-checkout scenarios.
- A valid coupon code `SAVE10` (10% off, min cart $20) and an expired code `OLDCODE`.
- Email service configured in stub mode to capture outbound messages.
- Feature flag `guest_checkout_enabled` set to `true`.

Setup method: API seeding via `/test/seed` endpoint before each test run.
Teardown method: API call to `/test/cleanup` after each test run.

---

## Scenario 1: Happy path — registered user checks out with credit card

- **Priority**: Critical
- **Tags**: `@smoke`, `@critical`, `@checkout`
- **Preconditions**: Cart contains one item (Widget A, qty 1, $25.00). User is logged in.
- **Steps**:
  1. Navigate to `/cart` → Verify: Cart displays Widget A, qty 1, subtotal $25.00
  2. Click "Proceed to Checkout" → Verify: Checkout page loads, shipping address pre-filled
  3. Confirm shipping address → Verify: Shipping options displayed with estimated delivery dates
  4. Select "Standard Shipping ($5.00)" → Verify: Order summary updates to $30.00
  5. Confirm saved credit card ending in 4242 → Verify: Payment section shows selected card
  6. Click "Place Order" → Verify:
     - UI: Order confirmation page displays order ID and "Thank you" message
     - API: `GET /api/orders/{id}` returns status `confirmed`, total `$30.00`
     - DB: `orders` table has new row with correct user ID, total, and status
     - Email: Confirmation email captured by stub with correct order ID
  7. Navigate to `/orders` → Verify: New order appears in order history with status "Confirmed"
- **Final state**: Order persisted, inventory decremented by 1, payment authorized, email sent
- **Estimated duration**: ~30 seconds

---

## Scenario 2: Guest checkout

- **Priority**: High
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: User is not logged in. Cart contains one item.
- **Steps**:
  1. Navigate to `/cart` → Click "Checkout as Guest"
  2. Enter email `guest@example.com` → Verify: Email field accepted, no account creation prompt
  3. Enter shipping address manually → Verify: Address validation passes
  4. Select shipping method → Verify: Order summary updates
  5. Enter new credit card details → Verify: Card input accepted
  6. Click "Place Order" → Verify:
     - UI: Confirmation page with order ID
     - DB: Order linked to guest email, no user account created
     - Email: Confirmation sent to `guest@example.com`
- **Final state**: Order created under guest email, no user account, email sent
- **Estimated duration**: ~35 seconds

---

## Scenario 3: Apply valid coupon code

- **Priority**: High
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: Cart subtotal is $50.00 (two items). User is logged in.
- **Steps**:
  1. Navigate to `/cart` → Verify: Subtotal $50.00
  2. Enter coupon code `SAVE10` and click "Apply" → Verify:
     - UI: Discount line shows "-$5.00", new subtotal $45.00
     - API: `GET /api/cart` returns `discount: 5.00`, `coupon: "SAVE10"`
  3. Proceed through checkout and place order → Verify:
     - Order total reflects discount ($45.00 + shipping)
     - DB: Order record includes coupon reference
- **Final state**: Order total includes 10% discount, coupon marked as used for this user
- **Estimated duration**: ~30 seconds

---

## Scenario 4: Apply expired coupon code

- **Priority**: Medium
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: Cart subtotal is $50.00. User is logged in.
- **Steps**:
  1. Navigate to `/cart` → Verify: Subtotal $50.00
  2. Enter coupon code `OLDCODE` and click "Apply" → Verify:
     - UI: Inline error "This coupon has expired"
     - Subtotal remains $50.00, no discount applied
     - API: `POST /api/cart/coupon` returns 422 with error message
- **Final state**: Cart unchanged, no discount applied
- **Estimated duration**: ~10 seconds

---

## Scenario 5: Payment declined

- **Priority**: High
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: Cart contains one item. User is logged in. Payment processor stub configured to decline card ending in 0002.
- **Steps**:
  1. Proceed through checkout to payment step
  2. Select card ending in 0002 → Click "Place Order" → Verify:
     - UI: Error message "Payment declined. Please try a different payment method."
     - No order created in DB
     - No confirmation email sent
     - Inventory not decremented
  3. Select a different card ending in 4242 → Click "Place Order" → Verify:
     - Order confirmation page displays
     - Order persisted in DB with correct payment method
- **Final state**: Order created on second attempt, first decline logged, inventory decremented once
- **Estimated duration**: ~35 seconds

---

## Scenario 6: Out-of-stock item removed at checkout

- **Priority**: High
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: Cart contains two items. Item B goes out of stock between cart and checkout (simulated by API stub or direct inventory update).
- **Steps**:
  1. Navigate to `/cart` → Verify: Both items displayed
  2. Trigger inventory change: set Item B stock to 0 via API
  3. Click "Proceed to Checkout" → Verify:
     - UI: Warning banner "Item B is no longer available and has been removed from your cart"
     - Cart now shows only Item A with updated subtotal
     - API: `GET /api/cart` returns only Item A
  4. Complete checkout with remaining item → Verify: Order placed for Item A only
- **Final state**: Order contains only Item A, Item B not charged, user notified
- **Estimated duration**: ~30 seconds

---

## Scenario 7: Update quantity in cart

- **Priority**: Medium
- **Tags**: `@regression`, `@checkout`
- **Preconditions**: Cart contains Widget A, qty 1, price $25.00.
- **Steps**:
  1. Navigate to `/cart` → Verify: Qty 1, subtotal $25.00
  2. Change quantity to 3 → Verify:
     - UI: Subtotal updates to $75.00
     - API: `GET /api/cart` returns qty 3, subtotal $75.00
  3. Change quantity to 0 → Verify:
     - UI: Item removed from cart, "Your cart is empty" message displayed
     - API: `GET /api/cart` returns empty items array
- **Final state**: Cart is empty
- **Estimated duration**: ~15 seconds

---

## Scenario 8: Session expiry during checkout

- **Priority**: Medium
- **Tags**: `@nightly`, `@checkout`
- **Preconditions**: Cart contains one item. User is logged in. Session TTL shortened to 30 seconds for testing.
- **Steps**:
  1. Navigate to checkout and reach the payment step
  2. Wait for session to expire (30 seconds)
  3. Click "Place Order" → Verify:
     - UI: Redirected to login page with message "Your session has expired. Please log in again."
     - After re-login, redirected back to checkout with cart intact
  4. Complete checkout → Verify: Order placed successfully
- **Final state**: Order created, cart preserved across re-authentication
- **Estimated duration**: ~60 seconds

---

## Scenario 9: Multiple shipping addresses

- **Priority**: Low
- **Tags**: `@nightly`, `@checkout`
- **Preconditions**: User has three saved addresses. Cart contains one item.
- **Steps**:
  1. Proceed to checkout → Verify: Default address pre-selected
  2. Click "Change address" → Verify: All three saved addresses listed
  3. Select a different address → Verify:
     - Shipping options update for new address
     - Order summary reflects new shipping cost
  4. Add a new address inline → Verify:
     - New address validated and saved to account
     - Shipping options update again
  5. Complete checkout with new address → Verify:
     - Order ships to newly added address
     - DB: Order record references correct address ID
- **Final state**: Order placed with new address, address saved to user profile
- **Estimated duration**: ~35 seconds

---

## Scenario 10: Back-button and navigation resilience

- **Priority**: Medium
- **Tags**: `@nightly`, `@checkout`
- **Preconditions**: Cart contains one item. User is logged in.
- **Steps**:
  1. Proceed to checkout → reach payment step
  2. Click browser back button → Verify: Returns to shipping step, selections preserved
  3. Click browser back button again → Verify: Returns to cart, items unchanged
  4. Click "Proceed to Checkout" again → Verify: Previous selections (address, shipping) restored
  5. Complete checkout → Verify: Order placed successfully, no duplicate orders
- **Final state**: Single order created, no duplicates, user selections preserved across navigation
- **Estimated duration**: ~30 seconds

---

## Coverage Summary

| Category | Count | Scenarios |
|---|---|---|
| Happy path | 1 | #1 |
| Alternative paths | 4 | #2, #3, #7, #9 |
| Error / recovery paths | 3 | #4, #5, #8 |
| Edge cases | 2 | #6, #10 |
| **Total** | **10** | |

### Execution cadence

| Tag | When to run | Scenarios |
|---|---|---|
| `@smoke` | Every PR / commit | #1 |
| `@critical` | Every PR / commit | #1 |
| `@regression` | Nightly or per-release | #2, #3, #4, #5, #6, #7 |
| `@nightly` | Nightly scheduled run | #8, #9, #10 |
