# Django E-Commerce Setup Guide

## Quick Start

### 1. Install Dependencies

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Environment Configuration

Create a `.env` file in the project root:

```bash
cp .env.example .env
```

Update the `.env` file with your configuration:
- Change `SECRET_KEY` to a new secure key
- Set `DEBUG=False` for production
- Configure database credentials
- Add Stripe API keys (from https://stripe.com)
- Add OpenAI API key (from https://openai.com)

### 3. Database Setup

```bash
# Run migrations
python manage.py migrate

# Create superuser account
python manage.py createsuperuser

# Create initial data (optional)
python manage.py shell
# Then run:
# from products.models import Category, Product
# Category.objects.create(name='Electronics', slug='electronics')
# Product.objects.create(name='Test Product', slug='test-product', price=99.99, stock=10, category_id=1)
```

### 4. Run Development Server

```bash
python manage.py runserver
```

Visit:
- **Admin Panel**: http://localhost:8000/admin
- **API**: http://localhost:8000/api/

### 5. Optional: Setup Redis & Celery

For async tasks, you'll need Redis:

#### Install Redis
- **Windows**: Download from https://github.com/microsoftarchive/redis/releases
- **macOS**: `brew install redis`
- **Linux**: `sudo apt-get install redis-server`

#### Start Redis
```bash
redis-server
```

#### Start Celery Worker
```bash
celery -A ecommerce worker -l info
```

#### Start Celery Beat (for scheduled tasks)
```bash
celery -A ecommerce beat -l info
```

## Docker Setup

If you have Docker installed:

```bash
# Build and run containers
docker-compose up -d

# Run migrations
docker-compose exec web python manage.py migrate

# Create superuser
docker-compose exec web python manage.py createsuperuser
```

## Project Structure

```
django_ecommerce/
├── ecommerce/              # Main Django project
│   ├── settings.py        # Project settings
│   ├── urls.py            # Main URL configuration
│   ├── asgi.py            # ASGI configuration (WebSocket)
│   ├── wsgi.py            # WSGI configuration
│   ├── celery.py          # Celery configuration
│   ├── payments.py        # Payment processing
│   ├── permissions.py     # Custom permissions
│   ├── pagination.py      # API pagination
│   ├── exceptions.py      # Custom exceptions
│   ├── signals.py         # Django signals
│   └── utils.py           # Utility functions
│
├── products/              # Product management
│   ├── models.py          # Category, Product, Review, ProductImage
│   ├── views.py           # API views
│   ├── serializers.py     # DRF serializers
│   ├── urls.py            # URL routing
│   ├── admin.py           # Admin configuration
│   └── tasks.py           # Celery tasks
│
├── cart/                  # Shopping cart
│   ├── models.py          # Cart, CartItem, Wishlist
│   ├── views.py           # API views
│   ├── serializers.py     # DRF serializers
│   └── urls.py            # URL routing
│
├── orders/                # Order management
│   ├── models.py          # Order, OrderItem
│   ├── views.py           # API views
│   ├── serializers.py     # DRF serializers
│   └── urls.py            # URL routing
│
├── coupons/               # Coupon system
│   ├── models.py          # Coupon
│   ├── views.py           # API views
│   └── urls.py            # URL routing
│
├── users/                 # User management
│   ├── models.py          # UserProfile
│   ├── views.py           # API views
│   ├── serializers.py     # DRF serializers
│   └── urls.py            # URL routing
│
├── analytics/             # Sales analytics
│   ├── models.py          # SalesAnalytics, ProductAnalytics, etc.
│   ├── views.py           # API views
│   ├── tasks.py           # Celery tasks
│   └── urls.py            # URL routing
│
├── recommendations/       # Product recommendations
│   ├── models.py          # ProductRecommendation, BrowsingHistory
│   ├── views.py           # API views
│   ├── tasks.py           # Celery tasks
│   └── urls.py            # URL routing
│
├── chat/                  # AI chatbot
│   ├── models.py          # ChatSession, ChatMessage
│   ├── consumers.py       # WebSocket consumer
│   ├── routing.py         # WebSocket routing
│   ├── views.py           # API views
│   ├── tasks.py           # Celery tasks
│   └── urls.py            # URL routing
│
├── manage.py              # Django CLI
├── requirements.txt       # Python dependencies
├── docker-compose.yml     # Docker configuration
├── Dockerfile             # Docker image definition
├── README.md              # Project documentation
├── API_DOCUMENTATION.md   # API endpoints documentation
└── SETUP.md              # This file
```

## Configuration Files

### settings.py
Main Django settings with:
- Database configuration (SQLite by default, PostgreSQL for production)
- Installed apps (products, cart, orders, etc.)
- Middleware setup
- REST Framework configuration
- JWT authentication
- CORS configuration
- Celery configuration
- Stripe and OpenAI settings

### Management Commands

Create sample data:
```bash
python manage.py loaddata sample_data.json
```

Run tests:
```bash
python manage.py test
```

Collect static files (production):
```bash
python manage.py collectstatic
```

Make migrations:
```bash
python manage.py makemigrations
```

## API Quick Reference

### Authentication
```bash
# Login
curl -X POST http://localhost:8000/api/auth/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "your_username", "password": "your_password"}'

# Response: {"access": "token...", "refresh": "token..."}
```

### Use Token
```bash
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  http://localhost:8000/api/products/
```

### Common Endpoints
- **Products**: GET /api/products/
- **Cart**: GET /api/cart/my_cart/
- **Orders**: GET /api/orders/
- **Wishlist**: GET /api/wishlist/my_wishlist/
- **Profile**: GET /api/users/profile/
- **Recommendations**: GET /api/recommendations/for_user/

See [API_DOCUMENTATION.md](API_DOCUMENTATION.md) for complete API reference.

## Production Deployment

### Environment Variables
Set these in production:
```
DEBUG=False
SECRET_KEY=your-secure-random-key
ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
DATABASE_URL=postgresql://user:password@localhost/dbname
STRIPE_PUBLIC_KEY=pk_live_...
STRIPE_SECRET_KEY=sk_live_...
OPENAI_API_KEY=sk-...
```

### Using Gunicorn
```bash
gunicorn ecommerce.wsgi:application --bind 0.0.0.0:8000
```

### Using Daphne (with WebSocket support)
```bash
daphne -b 0.0.0.0 -p 8000 ecommerce.asgi:application
```

### Nginx Configuration
```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /static/ {
        alias /path/to/staticfiles/;
    }

    location /media/ {
        alias /path/to/media/;
    }
}
```

## Troubleshooting

### Port Already in Use
```bash
# Find process using port 8000
lsof -i :8000

# Kill the process
kill -9 <PID>
```

### Database Errors
```bash
# Reset database (development only)
python manage.py migrate zero <app_name>
python manage.py migrate

# Or delete and recreate
rm db.sqlite3
python manage.py migrate
```

### Missing Dependencies
```bash
pip install -r requirements.txt
```

### Static Files Not Loading
```bash
python manage.py collectstatic --noinput
```

## Support & Documentation

- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Stripe Documentation](https://stripe.com/docs)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Celery Documentation](https://docs.celeryproject.io/)

## Next Steps

1. Configure your Stripe API keys
2. Set up OpenAI API key for chatbot
3. Configure email backend for notifications
4. Create sample products and categories
5. Test the full checkout flow
6. Deploy to production

Happy coding! 🚀
