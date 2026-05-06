# API Documentation

## Base URL
```
http://localhost:8000/api/
```

## Authentication
All API endpoints (except registration and login) require JWT authentication.

### Login
```
POST /api/auth/login/
Content-Type: application/json

{
  "username": "your_username",
  "password": "your_password"
}

Response:
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGc..."
}
```

### Using Token
```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Endpoints

### Products

#### List Products
```
GET /api/products/
Query Parameters:
  - page: integer (default: 1)
  - page_size: integer (default: 10)
  - category: integer
  - search: string
  - ordering: string (price, created_at, views, -created_at)
  - price_min: decimal
  - price_max: decimal

Response:
{
  "count": 100,
  "next": "http://localhost:8000/api/products/?page=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "name": "Product Name",
      "slug": "product-name",
      "price": "99.99",
      "discount_price": "79.99",
      "image": "http://...",
      "category": 1,
      "category_name": "Electronics",
      "stock": 10,
      "average_rating": 4.5,
      "discount_percentage": 20,
      "created_at": "2024-05-06T10:00:00Z"
    }
  ]
}
```

#### Get Product Details
```
GET /api/products/{slug}/

Response:
{
  "id": 1,
  "name": "Product Name",
  "slug": "product-name",
  "description": "Product description",
  "price": "99.99",
  "discount_price": "79.99",
  "stock": 10,
  "image": "http://...",
  "images": [
    {
      "id": 1,
      "image": "http://...",
      "alt_text": "Product image"
    }
  ],
  "category": {...},
  "is_active": true,
  "views": 150,
  "average_rating": 4.5,
  "discount_percentage": 20,
  "reviews": [
    {
      "id": 1,
      "user_name": "john_doe",
      "rating": 5,
      "title": "Great product",
      "comment": "Very satisfied",
      "is_verified_purchase": true,
      "helpful_count": 10,
      "created_at": "2024-05-06T10:00:00Z"
    }
  ],
  "created_at": "2024-05-06T10:00:00Z",
  "updated_at": "2024-05-06T10:00:00Z"
}
```

#### Add Review
```
POST /api/products/{slug}/add_review/
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "rating": 5,
  "title": "Great product",
  "comment": "Very satisfied with this product"
}

Response:
{
  "id": 1,
  "product": 1,
  "user": 1,
  "user_name": "john_doe",
  "rating": 5,
  "title": "Great product",
  "comment": "Very satisfied with this product",
  "is_verified_purchase": false,
  "helpful_count": 0,
  "created_at": "2024-05-06T10:00:00Z"
}
```

#### Get Trending Products
```
GET /api/products/trending/

Response: [similar to list products]
```

#### Get Top Rated Products
```
GET /api/products/top_rated/

Response: [similar to list products]
```

### Cart

#### Get Cart
```
GET /api/cart/my_cart/
Authorization: Bearer YOUR_ACCESS_TOKEN

Response:
{
  "id": 1,
  "user": 1,
  "items": [
    {
      "id": 1,
      "product": {...},
      "quantity": 2,
      "subtotal": "199.98",
      "added_at": "2024-05-06T10:00:00Z"
    }
  ],
  "total_items": 2,
  "total_price": "199.98",
  "created_at": "2024-05-06T10:00:00Z",
  "updated_at": "2024-05-06T10:00:00Z"
}
```

#### Add to Cart
```
POST /api/cart/add_item/
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "product_id": 1,
  "quantity": 2
}

Response:
{
  "id": 1,
  "product": {...},
  "quantity": 2,
  "subtotal": "199.98",
  "added_at": "2024-05-06T10:00:00Z"
}
```

#### Remove from Cart
```
POST /api/cart/remove_item/
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "cart_item_id": 1
}

Response:
{
  "message": "Item removed from cart"
}
```

#### Update Cart Item
```
POST /api/cart/update_item/
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "cart_item_id": 1,
  "quantity": 3
}

Response:
{
  "id": 1,
  "product": {...},
  "quantity": 3,
  "subtotal": "299.97",
  "added_at": "2024-05-06T10:00:00Z"
}
```

#### Clear Cart
```
POST /api/cart/clear_cart/
Authorization: Bearer YOUR_ACCESS_TOKEN

Response:
{
  "message": "Cart cleared"
}
```

### Orders

#### List Orders
```
GET /api/orders/
Authorization: Bearer YOUR_ACCESS_TOKEN
Query Parameters:
  - page: integer
  - status: string (pending, processing, shipped, delivered, cancelled, refunded)

Response: [list of orders]
```

#### Create Order
```
POST /api/orders/
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "shipping_name": "John Doe",
  "shipping_address": "123 Main St",
  "shipping_city": "New York",
  "shipping_state": "NY",
  "shipping_zip": "10001",
  "shipping_country": "US",
  "billing_name": "John Doe",
  "billing_address": "123 Main St",
  "billing_city": "New York",
  "billing_state": "NY",
  "billing_zip": "10001",
  "billing_country": "US",
  "payment_method": "stripe",
  "items": [
    {
      "product_id": 1,
      "quantity": 2
    }
  ],
  "coupon_code": "DISCOUNT10"
}

Response: [order details]
```

#### Track Order
```
GET /api/orders/{id}/track_order/
Authorization: Bearer YOUR_ACCESS_TOKEN

Response: [order details with current status]
```

#### Cancel Order
```
POST /api/orders/{id}/cancel_order/
Authorization: Bearer YOUR_ACCESS_TOKEN

Response: [updated order with cancelled status]
```

### Coupons

#### Validate Coupon
```
POST /api/coupons/validate_coupon/
Content-Type: application/json

{
  "code": "DISCOUNT10",
  "amount": 100.00
}

Response:
{
  "valid": true,
  "code": "DISCOUNT10",
  "discount": 10.00,
  "final_amount": 90.00
}
```

#### Get Active Coupons
```
GET /api/coupons/active_coupons/

Response: [list of active coupons]
```

### User Management

#### Register
```
POST /api/users/register/
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securepassword123",
  "password_confirm": "securepassword123",
  "first_name": "John",
  "last_name": "Doe"
}

Response:
{
  "id": 1,
  "username": "john_doe",
  "email": "john@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "profile": {...},
  "date_joined": "2024-05-06T10:00:00Z"
}
```

#### Get Profile
```
GET /api/users/profile/
Authorization: Bearer YOUR_ACCESS_TOKEN

Response: [user details with profile]
```

#### Update Profile
```
PUT /api/users/profile/
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "profile": {
    "phone": "5551234567",
    "address": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zip_code": "10001",
    "country": "US",
    "bio": "Software developer"
  }
}

Response: [updated user details]
```

### Recommendations

#### Get Personalized Recommendations
```
GET /api/recommendations/for_user/
Authorization: Bearer YOUR_ACCESS_TOKEN
Query Parameters:
  - page: integer

Response:
{
  "count": 10,
  "results": [
    {
      "id": 1,
      "user": 1,
      "recommended_product": {...},
      "recommendation_type": "collaborative",
      "score": 0.85,
      "clicked": false,
      "purchased": false,
      "created_at": "2024-05-06T10:00:00Z"
    }
  ]
}
```

#### Get Trending Products
```
GET /api/recommendations/trending/

Response: [list of trending recommendations]
```

### Analytics (Admin Only)

#### Get Daily Sales Analytics
```
GET /api/analytics/sales/daily/
Authorization: Bearer YOUR_ADMIN_TOKEN

Response:
{
  "count": 30,
  "results": [
    {
      "id": 1,
      "interval": "daily",
      "date": "2024-05-06",
      "total_orders": 45,
      "total_revenue": "4500.00",
      "total_items_sold": 150,
      "average_order_value": "100.00",
      "total_discounts": "450.00",
      "total_tax": "360.00",
      "created_at": "2024-05-06T10:00:00Z"
    }
  ]
}
```

#### Get Sales Summary
```
GET /api/analytics/sales/summary/
Authorization: Bearer YOUR_ADMIN_TOKEN

Response:
{
  "total_revenue": "150000.00",
  "total_orders": 1500,
  "total_items_sold": 5000,
  "average_order_value": "100.00"
}
```

#### Get Top Products
```
GET /api/analytics/products/top_products/
Authorization: Bearer YOUR_ADMIN_TOKEN

Response: [top selling products with analytics]
```

### Chat

#### Create Chat Session
```
POST /api/chat/sessions/
Content-Type: application/json

{
  "topic": "Product inquiry"
}

Response:
{
  "id": 1,
  "session_id": "abc123def456",
  "user": null,
  "topic": "Product inquiry",
  "sentiment": "neutral",
  "is_resolved": false,
  "rating": null,
  "feedback": "",
  "messages": [],
  "created_at": "2024-05-06T10:00:00Z"
}
```

#### Send Chat Message
```
POST /api/chat/messages/
Content-Type: application/json

{
  "session_id": "abc123def456",
  "content": "I have a question about product X",
  "language": "en"
}

Response:
{
  "id": 1,
  "session": 1,
  "message_type": "user",
  "content": "I have a question about product X",
  "language": "en",
  "intent": "",
  "confidence": 0.0,
  "recommended_product": null,
  "created_at": "2024-05-06T10:00:00Z"
}
```

#### End Chat Session
```
POST /api/chat/sessions/{id}/end_session/
Content-Type: application/json

{
  "rating": 5,
  "feedback": "Great support!"
}

Response: [updated session details]
```

## Error Responses

### 400 Bad Request
```json
{
  "detail": "Invalid input",
  "status_code": 400
}
```

### 401 Unauthorized
```json
{
  "detail": "Authentication credentials were not provided.",
  "status_code": 401
}
```

### 403 Forbidden
```json
{
  "detail": "You do not have permission to perform this action.",
  "status_code": 403
}
```

### 404 Not Found
```json
{
  "detail": "Not found.",
  "status_code": 404
}
```

### 500 Internal Server Error
```json
{
  "detail": "Internal server error",
  "status_code": 500
}
```
