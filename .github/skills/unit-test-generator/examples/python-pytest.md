# Example: Generating pytest Tests for a Python Function

A complete walkthrough of generating unit tests for a Python module using pytest.

---

## Source Code Under Test

```python
# src/inventory.py

from dataclasses import dataclass
from typing import Optional


@dataclass
class Product:
    sku: str
    name: str
    quantity: int
    price: float


class InsufficientStockError(Exception):
    pass


class ProductNotFoundError(Exception):
    pass


class InventoryService:
    def __init__(self, products: Optional[list[Product]] = None):
        self._products: dict[str, Product] = {}
        for p in (products or []):
            self._products[p.sku] = p

    def get_product(self, sku: str) -> Product:
        """Look up a product by SKU. Raises ProductNotFoundError if missing."""
        if not sku or not sku.strip():
            raise ValueError("SKU must not be empty")
        product = self._products.get(sku)
        if product is None:
            raise ProductNotFoundError(f"Product '{sku}' not found")
        return product

    def restock(self, sku: str, amount: int) -> int:
        """Add stock to an existing product. Returns the new quantity."""
        if amount <= 0:
            raise ValueError("Restock amount must be positive")
        product = self.get_product(sku)
        product.quantity += amount
        return product.quantity

    def sell(self, sku: str, quantity: int) -> float:
        """Sell a quantity of a product. Returns the total cost."""
        if quantity <= 0:
            raise ValueError("Quantity must be positive")
        product = self.get_product(sku)
        if product.quantity < quantity:
            raise InsufficientStockError(
                f"Requested {quantity} but only {product.quantity} available"
            )
        product.quantity -= quantity
        return round(product.price * quantity, 2)
```

---

## Step 1: Analyze the Code

| Aspect | Findings |
|--------|----------|
| **Inputs** | SKU strings, integer amounts, list of Product objects |
| **Outputs** | Product objects, integer quantities, float totals |
| **Error paths** | `ValueError` (empty SKU, non-positive amounts), `ProductNotFoundError`, `InsufficientStockError` |
| **Branches** | Empty SKU check, product existence check, stock sufficiency check |
| **Dependencies** | None (self-contained; no external I/O) |
| **State** | Internal `_products` dict mutated by `restock` and `sell` |

---

## Step 2: Plan Test Cases

### `get_product`
- Returns product when SKU exists
- Raises `ProductNotFoundError` when SKU is missing
- Raises `ValueError` when SKU is empty string
- Raises `ValueError` when SKU is whitespace-only

### `restock`
- Increases quantity and returns new total
- Raises `ValueError` when amount is zero
- Raises `ValueError` when amount is negative
- Raises `ProductNotFoundError` when SKU is missing

### `sell`
- Deducts stock and returns correct total cost
- Handles selling entire remaining stock (boundary)
- Raises `InsufficientStockError` when requesting more than available
- Raises `ValueError` when quantity is zero
- Raises `ValueError` when quantity is negative
- Verifies the total is rounded to two decimal places

---

## Step 3: Generated Test File

```python
# tests/test_inventory.py

import pytest
from src.inventory import (
    InventoryService,
    InsufficientStockError,
    Product,
    ProductNotFoundError,
)


@pytest.fixture
def sample_products():
    return [
        Product(sku="WIDGET-01", name="Widget", quantity=10, price=9.99),
        Product(sku="GADGET-02", name="Gadget", quantity=5, price=24.50),
    ]


@pytest.fixture
def service(sample_products):
    return InventoryService(products=sample_products)


class TestGetProduct:
    def test_returns_product_when_sku_exists(self, service):
        product = service.get_product("WIDGET-01")

        assert product.sku == "WIDGET-01"
        assert product.name == "Widget"

    def test_raises_not_found_when_sku_missing(self, service):
        with pytest.raises(ProductNotFoundError, match="UNKNOWN"):
            service.get_product("UNKNOWN")

    def test_raises_value_error_when_sku_is_empty(self, service):
        with pytest.raises(ValueError, match="SKU must not be empty"):
            service.get_product("")

    def test_raises_value_error_when_sku_is_whitespace(self, service):
        with pytest.raises(ValueError, match="SKU must not be empty"):
            service.get_product("   ")


class TestRestock:
    def test_increases_quantity_and_returns_new_total(self, service):
        new_quantity = service.restock("WIDGET-01", 5)

        assert new_quantity == 15
        assert service.get_product("WIDGET-01").quantity == 15

    def test_raises_value_error_when_amount_is_zero(self, service):
        with pytest.raises(ValueError, match="Restock amount must be positive"):
            service.restock("WIDGET-01", 0)

    def test_raises_value_error_when_amount_is_negative(self, service):
        with pytest.raises(ValueError, match="Restock amount must be positive"):
            service.restock("WIDGET-01", -3)

    def test_raises_not_found_when_sku_missing(self, service):
        with pytest.raises(ProductNotFoundError):
            service.restock("UNKNOWN", 5)


class TestSell:
    def test_deducts_stock_and_returns_total(self, service):
        total = service.sell("WIDGET-01", 3)

        assert total == 29.97
        assert service.get_product("WIDGET-01").quantity == 7

    def test_sells_entire_remaining_stock(self, service):
        total = service.sell("GADGET-02", 5)

        assert total == 122.50
        assert service.get_product("GADGET-02").quantity == 0

    def test_raises_insufficient_stock_when_over_limit(self, service):
        with pytest.raises(InsufficientStockError, match="Requested 20 but only 10"):
            service.sell("WIDGET-01", 20)

    def test_raises_value_error_when_quantity_is_zero(self, service):
        with pytest.raises(ValueError, match="Quantity must be positive"):
            service.sell("WIDGET-01", 0)

    def test_raises_value_error_when_quantity_is_negative(self, service):
        with pytest.raises(ValueError, match="Quantity must be positive"):
            service.sell("WIDGET-01", -1)

    def test_total_rounds_to_two_decimal_places(self, service):
        # 3 * 9.99 = 29.97 (exact with round)
        total = service.sell("WIDGET-01", 3)
        assert total == pytest.approx(29.97)


class TestInventoryServiceInit:
    def test_initializes_empty_when_no_products_given(self):
        service = InventoryService()

        with pytest.raises(ProductNotFoundError):
            service.get_product("anything")

    def test_initializes_with_provided_products(self, sample_products):
        service = InventoryService(products=sample_products)

        assert service.get_product("WIDGET-01").name == "Widget"
        assert service.get_product("GADGET-02").name == "Gadget"
```

---

## Key Decisions Explained

| Decision | Rationale |
|----------|-----------|
| **Used a pytest fixture for `service`** | Fresh instance per test prevents shared mutable state |
| **Grouped tests by method in classes** | Organizes output and makes it easy to run a subset |
| **Tested both `total` return value and `quantity` side effect** | `sell` has two observable outcomes; both need verification |
| **Used `match=` in `pytest.raises`** | Verifies the error message, not just the exception type |
| **Included boundary test (sell entire stock)** | Off-by-one at quantity == available stock is a common bug |
| **No mocks needed** | `InventoryService` has no external dependencies; test it directly |
