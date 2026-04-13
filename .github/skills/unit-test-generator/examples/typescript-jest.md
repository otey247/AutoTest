# Example: Generating Jest Tests for a TypeScript Service

A complete walkthrough of generating unit tests for a TypeScript module using Jest.

## Table of Contents

- [Source Code Under Test](#source-code-under-test)
- [Step 1: Analyze the Code](#step-1-analyze-the-code)
- [Step 2: Plan Test Cases](#step-2-plan-test-cases)
- [Step 3: Generated Test File](#step-3-generated-test-file)
- [Key Decisions Explained](#key-decisions-explained)

---

## Source Code Under Test

```typescript
// src/orderService.ts

export interface Order {
  id: string;
  customerId: string;
  items: OrderItem[];
  status: "pending" | "confirmed" | "shipped" | "cancelled";
}

export interface OrderItem {
  productId: string;
  quantity: number;
  unitPrice: number;
}

export interface OrderRepository {
  findById(id: string): Promise<Order | null>;
  save(order: Order): Promise<void>;
}

export interface PaymentGateway {
  charge(customerId: string, amount: number): Promise<{ success: boolean; transactionId: string }>;
}

export class OrderNotFoundError extends Error {
  constructor(id: string) {
    super(`Order ${id} not found`);
    this.name = "OrderNotFoundError";
  }
}

export class InvalidOrderError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "InvalidOrderError";
  }
}

export function calculateTotal(items: OrderItem[]): number {
  if (items.length === 0) {
    throw new InvalidOrderError("Order must have at least one item");
  }
  const total = items.reduce(
    (sum, item) => sum + item.quantity * item.unitPrice,
    0
  );
  return Math.round(total * 100) / 100;
}

export async function confirmOrder(
  orderId: string,
  repo: OrderRepository,
  payment: PaymentGateway
): Promise<string> {
  if (!orderId || orderId.trim() === "") {
    throw new InvalidOrderError("Order ID is required");
  }

  const order = await repo.findById(orderId);
  if (!order) {
    throw new OrderNotFoundError(orderId);
  }

  if (order.status !== "pending") {
    throw new InvalidOrderError(
      `Cannot confirm order in '${order.status}' status`
    );
  }

  const total = calculateTotal(order.items);
  const result = await payment.charge(order.customerId, total);

  if (!result.success) {
    throw new Error("Payment failed");
  }

  order.status = "confirmed";
  await repo.save(order);

  return result.transactionId;
}
```

---

## Step 1: Analyze the Code

| Aspect | Findings |
|--------|----------|
| **Inputs** | Order ID string, OrderRepository, PaymentGateway |
| **Outputs** | `calculateTotal` → number; `confirmOrder` → transaction ID string |
| **Side effects** | `repo.save()` persists the updated order; `payment.charge()` processes payment |
| **Branches** | Empty items, empty/whitespace ID, order not found, wrong status, payment failure |
| **Dependencies** | `OrderRepository` (async I/O), `PaymentGateway` (external service) — both need mocking |
| **Error paths** | `InvalidOrderError`, `OrderNotFoundError`, generic `Error` for payment failure |

---

## Step 2: Plan Test Cases

### `calculateTotal`
- Returns correct total for single item
- Returns correct total for multiple items
- Rounds to two decimal places
- Throws `InvalidOrderError` when items array is empty

### `confirmOrder`
- Returns transaction ID on successful confirmation
- Updates order status to "confirmed" and saves
- Throws `InvalidOrderError` when order ID is empty
- Throws `InvalidOrderError` when order ID is whitespace
- Throws `OrderNotFoundError` when order does not exist
- Throws `InvalidOrderError` when order status is not "pending"
- Throws generic Error when payment fails
- Does not save order when payment fails

---

## Step 3: Generated Test File

```typescript
// src/__tests__/orderService.test.ts

import {
  calculateTotal,
  confirmOrder,
  InvalidOrderError,
  Order,
  OrderItem,
  OrderNotFoundError,
  OrderRepository,
  PaymentGateway,
} from "../orderService";

// --- Test Data Factory ---

function buildItem(overrides: Partial<OrderItem> = {}): OrderItem {
  return {
    productId: "prod-1",
    quantity: 1,
    unitPrice: 10.0,
    ...overrides,
  };
}

function buildOrder(overrides: Partial<Order> = {}): Order {
  return {
    id: "order-1",
    customerId: "cust-1",
    items: [buildItem()],
    status: "pending",
    ...overrides,
  };
}

// --- Mock Dependencies ---

function createMockRepo(): jest.Mocked<OrderRepository> {
  return {
    findById: jest.fn(),
    save: jest.fn().mockResolvedValue(undefined),
  };
}

function createMockPayment(): jest.Mocked<PaymentGateway> {
  return {
    charge: jest.fn().mockResolvedValue({
      success: true,
      transactionId: "txn-abc",
    }),
  };
}

// --- Tests ---

describe("calculateTotal", () => {
  it("returns the total for a single item", () => {
    const items = [buildItem({ quantity: 3, unitPrice: 5.0 })];

    const total = calculateTotal(items);

    expect(total).toBe(15.0);
  });

  it("returns the combined total for multiple items", () => {
    const items = [
      buildItem({ quantity: 2, unitPrice: 10.0 }),
      buildItem({ quantity: 1, unitPrice: 7.5 }),
    ];

    const total = calculateTotal(items);

    expect(total).toBe(27.5);
  });

  it("rounds the total to two decimal places", () => {
    const items = [buildItem({ quantity: 3, unitPrice: 1.33 })];

    const total = calculateTotal(items);

    // 3 * 1.33 = 3.99
    expect(total).toBe(3.99);
  });

  it("throws InvalidOrderError when items array is empty", () => {
    expect(() => calculateTotal([])).toThrow(InvalidOrderError);
    expect(() => calculateTotal([])).toThrow("Order must have at least one item");
  });
});

describe("confirmOrder", () => {
  let mockRepo: jest.Mocked<OrderRepository>;
  let mockPayment: jest.Mocked<PaymentGateway>;

  beforeEach(() => {
    mockRepo = createMockRepo();
    mockPayment = createMockPayment();
  });

  it("returns the transaction ID on successful confirmation", async () => {
    const order = buildOrder();
    mockRepo.findById.mockResolvedValue(order);

    const txnId = await confirmOrder("order-1", mockRepo, mockPayment);

    expect(txnId).toBe("txn-abc");
  });

  it("updates status to confirmed and saves the order", async () => {
    const order = buildOrder();
    mockRepo.findById.mockResolvedValue(order);

    await confirmOrder("order-1", mockRepo, mockPayment);

    expect(order.status).toBe("confirmed");
    expect(mockRepo.save).toHaveBeenCalledWith(
      expect.objectContaining({ id: "order-1", status: "confirmed" })
    );
  });

  it("charges the correct amount to the correct customer", async () => {
    const order = buildOrder({
      customerId: "cust-42",
      items: [buildItem({ quantity: 2, unitPrice: 15.0 })],
    });
    mockRepo.findById.mockResolvedValue(order);

    await confirmOrder("order-1", mockRepo, mockPayment);

    expect(mockPayment.charge).toHaveBeenCalledWith("cust-42", 30.0);
  });

  it("throws InvalidOrderError when order ID is empty", async () => {
    await expect(
      confirmOrder("", mockRepo, mockPayment)
    ).rejects.toThrow(InvalidOrderError);
  });

  it("throws InvalidOrderError when order ID is whitespace", async () => {
    await expect(
      confirmOrder("   ", mockRepo, mockPayment)
    ).rejects.toThrow("Order ID is required");
  });

  it("throws OrderNotFoundError when order does not exist", async () => {
    mockRepo.findById.mockResolvedValue(null);

    await expect(
      confirmOrder("missing-id", mockRepo, mockPayment)
    ).rejects.toThrow(OrderNotFoundError);
  });

  it("throws InvalidOrderError when order status is not pending", async () => {
    const order = buildOrder({ status: "shipped" });
    mockRepo.findById.mockResolvedValue(order);

    await expect(
      confirmOrder("order-1", mockRepo, mockPayment)
    ).rejects.toThrow("Cannot confirm order in 'shipped' status");
  });

  it("throws Error when payment fails", async () => {
    const order = buildOrder();
    mockRepo.findById.mockResolvedValue(order);
    mockPayment.charge.mockResolvedValue({
      success: false,
      transactionId: "",
    });

    await expect(
      confirmOrder("order-1", mockRepo, mockPayment)
    ).rejects.toThrow("Payment failed");
  });

  it("does not save the order when payment fails", async () => {
    const order = buildOrder();
    mockRepo.findById.mockResolvedValue(order);
    mockPayment.charge.mockResolvedValue({
      success: false,
      transactionId: "",
    });

    await expect(
      confirmOrder("order-1", mockRepo, mockPayment)
    ).rejects.toThrow();

    expect(mockRepo.save).not.toHaveBeenCalled();
  });

  it("does not charge payment when order is not pending", async () => {
    const order = buildOrder({ status: "cancelled" });
    mockRepo.findById.mockResolvedValue(order);

    await expect(
      confirmOrder("order-1", mockRepo, mockPayment)
    ).rejects.toThrow();

    expect(mockPayment.charge).not.toHaveBeenCalled();
  });
});
```

---

## Key Decisions Explained

| Decision | Rationale |
|----------|-----------|
| **Created `buildItem` and `buildOrder` factories** | Eliminates boilerplate; tests only specify the fields relevant to each scenario |
| **Created `createMockRepo` and `createMockPayment` helpers** | Centralizes mock setup; easy to maintain when interfaces change |
| **Used `beforeEach` to reset mocks** | Fresh mocks per test prevent cross-test contamination |
| **Tested `calculateTotal` separately** | Pure function with no dependencies — tested directly without mocks for clearer, faster tests |
| **Verified `save` is not called on payment failure** | Confirms the function doesn't persist an order when the transaction fails (negative behavioral check) |
| **Verified `charge` is not called for non-pending orders** | Confirms early validation prevents unnecessary payment gateway calls |
| **Used both `.toThrow(ErrorClass)` and `.toThrow("message")`** | Type check verifies the error category; message check verifies the specific scenario |
