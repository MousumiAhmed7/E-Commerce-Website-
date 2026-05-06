# Django E-Commerce Application

A modern, feature-rich Django e-commerce platform with AI-powered product recommendations and chatbot support.

## Features

### Core E-Commerce
- **Product Management**: Browse, search, and filter products by category
- **Shopping Cart**: Add/remove items, manage quantities
- **Wishlist**: Save favorite products for later
- **Secure Checkout**: Complete order processing with multiple payment methods
- **Order Tracking**: Real-time order status updates
- **Product Reviews**: Rate and review products with verified purchase badges

### Advanced Features
- **Coupon System**: Create and manage discount codes with flexible discount types
- **Admin Analytics**: Comprehensive sales, product, and customer analytics
- **Product Recommendations**: AI-powered collaborative filtering and content-based recommendations
- **AI Chatbot**: Real-time chat support with product recommendations
- **User Accounts**: Extended profiles with address management and preferences

### Payment & Integration
- **Stripe Integration**: Secure payment processing
- **Email Notifications**: Order confirmations and updates
- **WebSocket Support**: Real-time chat functionality
- **Celery Background Tasks**: Async processing for emails and analytics

## Project Structure

```
ecommerce/
├── ecommerce/          # Main Django project settings
├── products/           # Product management app
├── cart/              # Shopping cart and wishlist
├── orders/            # Order management
├── coupons/           # Discount codes
├── users/             # User management and profiles
├── analytics/         # Sales and user analytics
├── recommendations/   # Product recommendations engine
├── chat/              # AI chatbot with WebSocket support
├── templates/         # HTML templates
├── static/            # CSS, JavaScript, images
└── manage.py          # Django management script
```

## Installation & Setup

### 1. Clone and Install Dependencies

```bash
cd django_ecommerce
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configure Settings

Edit `ecommerce/settings.py` to set:
- `SECRET_KEY`: Generate a new secret key
- `DEBUG`: Set to `False` in production
- Database settings (default is SQLite, configure PostgreSQL for production)
- Stripe API keys
- OpenAI API key for chatbot
- Email configuration

### 3. Database Setup

```bash
python manage.py migrate
python manage.py createsuperuser
```

### 4. Create Sample Data (Optional)

```bash
python manage.py loaddata sample_data.json
```

### 5. Run Development Server

```bash
python manage.py runserver
```

Visit `http://localhost:8000/admin` to access the admin panel.

## API Endpoints

### Products
- `GET /api/products/` - List all products
- `GET /api/products/{slug}/` - Product details
- `GET /api/products/{slug}/reviews/` - Product reviews
- `POST /api/products/{slug}/add_review/` - Add review
- `GET /api/products/trending/` - Trending products
- `GET /api/products/top_rated/` - Top rated products

### Cart & Wishlist
- `GET /api/cart/my_cart/` - Get current cart
- `POST /api/cart/add_item/` - Add item to cart
- `POST /api/cart/remove_item/` - Remove item from cart
- `POST /api/cart/clear_cart/` - Clear entire cart
- `GET /api/wishlist/my_wishlist/` - Get wishlist
- `POST /api/wishlist/add_product/` - Add to wishlist
- `POST /api/wishlist/remove_product/` - Remove from wishlist

### Orders
- `GET /api/orders/` - List user orders
- `POST /api/orders/` - Create new order
- `GET /api/orders/{id}/` - Order details
- `POST /api/orders/{id}/cancel_order/` - Cancel order
- `POST /api/orders/{id}/track_order/` - Track order

### Coupons
- `POST /api/coupons/validate_coupon/` - Validate coupon code
- `GET /api/coupons/active_coupons/` - Get active coupons

### Users
- `POST /api/users/register/` - Register new user
- `GET /api/users/profile/` - Get user profile
- `PUT /api/users/profile/` - Update user profile
- `POST /api/users/change_password/` - Change password

### Recommendations
- `GET /api/recommendations/for_user/` - Get personalized recommendations
- `GET /api/recommendations/trending/` - Get trending products
- `GET /api/recommendations/related/` - Get related products

### Analytics (Admin Only)
- `GET /api/analytics/sales/` - Sales analytics
- `GET /api/analytics/products/` - Product analytics
- `GET /api/analytics/categories/` - Category analytics
- `GET /api/analytics/users/` - User analytics

### Chat
- `POST /api/chat/sessions/` - Create chat session
- `GET /api/chat/sessions/` - Get user chat sessions
- `POST /api/chat/messages/` - Send chat message
- `WS /ws/chat/{session_id}/` - WebSocket chat connection

## Authentication

The API uses JWT (JSON Web Tokens) for authentication:

```bash
# Get tokens
curl -X POST http://localhost:8000/api/auth/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "user", "password": "password"}'

# Use access token in headers
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  http://localhost:8000/api/products/
```

## Database Models

### Products App
- `Category` - Product categories
- `Product` - Product listings
- `ProductImage` - Multiple images per product
- `Review` - Product reviews and ratings

### Cart App
- `Cart` - Shopping cart per user
- `CartItem` - Items in cart
- `Wishlist` - User wishlists

### Orders App
- `Order` - Customer orders
- `OrderItem` - Items in an order

### Coupons App
- `Coupon` - Discount codes

### Users App
- `UserProfile` - Extended user information

### Analytics App
- `SalesAnalytics` - Daily/monthly sales data
- `ProductAnalytics` - Product performance metrics
- `CategoryAnalytics` - Category performance
- `UserAnalytics` - Customer analytics

### Recommendations App
- `ProductRecommendation` - AI recommendations
- `BrowsingHistory` - User browsing history
- `CoProductRelation` - Products frequently bought together

### Chat App
- `ChatSession` - Chat conversation sessions
- `ChatMessage` - Individual messages
- `ChatIntentMapping` - Chatbot intent mappings

## Configuration

### Celery (Async Tasks)

Celery is configured for background tasks like sending emails and updating analytics.

```bash
# Start Celery worker
celery -A ecommerce worker -l info

# Start Celery beat (scheduled tasks)
celery -A ecommerce beat -l info
```

### Redis

Redis is required for Celery and WebSocket support:

```bash
# Install and run Redis (Windows/Mac/Linux)
redis-server
```

### Email Configuration

Configure SMTP settings in `ecommerce/settings.py`:

```python
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_HOST_USER = 'your-email@gmail.com'
EMAIL_HOST_PASSWORD = 'your-app-password'
```

## Deployment

### Using Gunicorn

```bash
gunicorn ecommerce.wsgi:application --bind 0.0.0.0:8000
```

### Using Daphne (WebSocket support)

```bash
daphne -b 0.0.0.0 -p 8000 ecommerce.asgi:application
```

### Environment Variables

Create a `.env` file:

```env
SECRET_KEY=your-secret-key
DEBUG=False
ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
DATABASE_URL=postgresql://user:password@localhost/dbname
STRIPE_PUBLIC_KEY=pk_live_...
STRIPE_SECRET_KEY=sk_live_...
OPENAI_API_KEY=sk-...
```

## Testing

Run the test suite:

```bash
python manage.py test
```

With coverage:

```bash
coverage run --source='.' manage.py test
coverage report
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For support, email support@example.com or open an issue in the repository.

## Roadmap

- [ ] Mobile app (React Native)
- [ ] Advanced inventory management
- [ ] Multi-vendor support
- [ ] Subscription products
- [ ] Advanced search with Elasticsearch
- [ ] GraphQL API
- [ ] Social features (wishlist sharing, reviews)
- [ ] Augmented reality product preview
