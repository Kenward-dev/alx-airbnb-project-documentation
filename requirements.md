# Airbnb Clone Backend API Documentation

## Overview

This document outlines the RESTful API specifications for the Airbnb clone backend application, focusing on three core features: User Management, Property Listings, and Booking Management, with brief mentions of other essential features.

## Base URL

```
https://api.airbnbclone.com/v1
```

## General Information

### Authentication

The API uses JWT (JSON Web Token) for authentication:
- Access tokens expire after 1 hour
- Refresh tokens expire after 7 days
- Include token in header: `Authorization: Bearer {jwt_token}`

### Error Handling

All endpoints return standardized error responses:

```json
{
  "status": "error",
  "code": 400,
  "message": "Error description",
  "details": {} 
}
```

### Rate Limiting

- **Standard users**: 100 requests per minute
- **Premium users**: 300 requests per minute
- **Admin users**: 500 requests per minute

### Performance Criteria

- **Response time**: 95% of API requests complete in < 300ms
- **Availability**: 99.9% uptime (SLA)
- **Scalability**: Supports up to 10,000 concurrent users
- **Throughput**: Handles 1,000 requests per second at peak load

## Core Feature 1: User Management API

### Register User

Creates a new user account.

**Endpoint:** `POST /users/register`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "firstName": "John",
  "lastName": "Doe",
  "phoneNumber": "+1234567890",
  "dateOfBirth": "1990-01-01",
  "accountType": "guest" // "guest" or "host"
}
```

**Validation Rules:**
- Email: Valid format, unique in system
- Password: Min 8 chars, must contain uppercase, lowercase, number, special char
- First/Last Name: Required, 2-50 chars
- Date of Birth: Valid date, user must be 18+
- Account Type: Must be either "guest" or "host"

**Response: `201 Created`**
```json
{
  "status": "success",
  "data": {
    "userId": "usr_12345abcde",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "accountType": "guest",
    "createdAt": "2025-05-07T10:30:00Z"
  }
}
```

### Login

Authenticates a user and returns access tokens.

**Endpoint:** `POST /users/login`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response: `200 OK`**
```json
{
  "status": "success",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600,
    "userId": "usr_12345abcde"
  }
}
```

### OAuth Login

**Endpoint:** `POST /users/oauth/{provider}`  
**Supported Providers:** `google`, `facebook`, `apple`

### Update User Profile

**Endpoint:** `PATCH /users/profile`  
**Auth Required:** Yes

### Additional User Management Endpoints
- `GET /users/profile` - Retrieve user profile
- `POST /users/profile/image` - Upload profile image
- `POST /users/verifications/{type}` - Request verification
- `POST /users/password` - Change password
- `POST /users/password/reset-request` - Request password reset
- `DELETE /users/account` - Delete account

## Core Feature 2: Property Listings API

### Create Listing

Creates a new property listing.

**Endpoint:** `POST /listings`

**Auth Required:** Yes (Host account type)

**Request Body:**
```json
{
  "title": "Beachfront Villa with Private Pool",
  "description": "Luxurious 3-bedroom villa with spectacular ocean views...",
  "propertyType": "house",
  "roomType": "entire_place",
  "location": {
    "address": "123 Ocean Drive",
    "city": "Malibu",
    "state": "California",
    "country": "United States",
    "zipCode": "90265",
    "coordinates": {
      "latitude": 34.0259,
      "longitude": -118.7798
    }
  },
  "amenities": ["wifi", "pool", "kitchen", "ac", "beachfront"],
  "capacity": {
    "guests": 6,
    "bedrooms": 3,
    "beds": 4,
    "bathrooms": 2.5
  },
  "pricing": {
    "basePrice": 250,
    "currency": "USD",
    "cleaningFee": 100,
    "securityDeposit": 500
  },
  "availability": {
    "minStay": 2,
    "maxStay": 30
  },
  "rules": {
    "petsAllowed": false,
    "smokingAllowed": false,
    "checkInFrom": "15:00",
    "checkOutBefore": "11:00"
  }
}
```

**Validation Rules:**
- Title: Required, 10-100 chars
- Description: Required, 100-5000 chars
- Property Type: Required, valid enum value
- Location: Required with valid address and coordinates
- Capacity: Required, valid ranges (guests: 1-20, bedrooms: 1-20, etc.)
- Pricing: Required, valid currency and pricing values

**Response: `201 Created`**
```json
{
  "status": "success",
  "data": {
    "listingId": "lst_12345abcde",
    "title": "Beachfront Villa with Private Pool",
    "status": "draft",
    "createdAt": "2025-05-07T14:30:00Z",
    "completionPercentage": 85
  }
}
```

### Upload Listing Photos

**Endpoint:** `POST /listings/{listingId}/photos`

**Auth Required:** Yes (Host account type, must own listing)

**Content-Type:** `multipart/form-data`

**Validation Rules:**
- File size: Max 10MB per photo
- Image dimensions: Min 800x600px
- File types: JPG, PNG, WebP only
- Max number of photos per request: 10
- Max total photos per listing: 50

### Get Listing Availability

**Endpoint:** `GET /listings/{listingId}/availability`

**Query Parameters:**
- `startDate`: Start date (YYYY-MM-DD)
- `endDate`: End date (YYYY-MM-DD)

**Response: `200 OK`**
```json
{
  "status": "success",
  "data": {
    "listingId": "lst_12345abcde",
    "availability": [
      {
        "date": "2025-05-10",
        "available": true,
        "price": 250
      },
      {
        "date": "2025-05-11",
        "available": true,
        "price": 275
      },
      {
        "date": "2025-05-12",
        "available": false,
        "reason": "booked"
      }
    ]
  }
}
```

### Additional Property Listing Endpoints
- `GET /listings/{listingId}` - Get listing details
- `PATCH /listings/{listingId}` - Update listing
- `DELETE /listings/{listingId}` - Delete listing
- `POST /listings/{listingId}/availability` - Update availability
- `PATCH /listings/{listingId}/pricing` - Update pricing
- `GET /listings/{listingId}/reviews` - Get listing reviews

## Core Feature 3: Booking Management API

### Create Booking

Creates a new booking for a property.

**Endpoint:** `POST /bookings`

**Auth Required:** Yes

**Request Body:**
```json
{
  "listingId": "lst_12345abcde",
  "checkIn": "2025-06-15",
  "checkOut": "2025-06-20",
  "guests": {
    "adults": 2,
    "children": 1,
    "infants": 0,
    "pets": 0
  },
  "specialRequests": "We'll be arriving late, around 9 PM.",
  "purposeOfStay": "vacation"
}
```

**Validation Rules:**
- Listing ID: Must exist and be active
- Check-in/Check-out: Valid dates, check-in must be before check-out
- Dates: Must be available for the listing
- Guests: Total number must not exceed listing capacity
- Stay Duration: Must meet minimum and maximum stay requirements

**Response: `201 Created`**
```json
{
  "status": "success",
  "data": {
    "bookingId": "bkg_12345abcde",
    "listingId": "lst_12345abcde",
    "checkIn": "2025-06-15",
    "checkOut": "2025-06-20",
    "status": "pending",
    "guests": {
      "adults": 2,
      "children": 1,
      "infants": 0,
      "pets": 0
    },
    "pricing": {
      "currency": "USD",
      "nightlyRates": [
        {"date": "2025-06-15", "amount": 250},
        {"date": "2025-06-16", "amount": 250},
        {"date": "2025-06-17", "amount": 250},
        {"date": "2025-06-18", "amount": 250},
        {"date": "2025-06-19", "amount": 275}
      ],
      "subtotal": 1275,
      "cleaningFee": 100,
      "serviceFee": 175,
      "taxes": 156,
      "total": 1706
    },
    "paymentStatus": "pending",
    "expiresAt": "2025-05-07T15:30:00Z",
    "createdAt": "2025-05-07T14:30:00Z"
  }
}
```

### Get Booking

Retrieves booking details.

**Endpoint:** `GET /bookings/{bookingId}`

**Auth Required:** Yes (Booking guest or listing host)

**Response: `200 OK`**
```json
{
  "status": "success",
  "data": {
    "bookingId": "bkg_12345abcde",
    "listing": {
      "listingId": "lst_12345abcde",
      "title": "Beachfront Villa with Private Pool",
      "primaryPhoto": "https://storage.airbnbclone.com/listings/lst_12345abcde/photo1.jpg",
      "location": {
        "city": "Malibu",
        "state": "California",
        "country": "United States"
      }
    },
    "host": {
      "userId": "usr_67890fghij",
      "firstName": "Jane",
      "profileImage": "https://storage.airbnbclone.com/profiles/67890fghij.jpg"
    },
    "guest": {
      "userId": "usr_12345abcde",
      "firstName": "John",
      "profileImage": "https://storage.airbnbclone.com/profiles/12345abcde.jpg"
    },
    "checkIn": "2025-06-15",
    "checkOut": "2025-06-20",
    "status": "confirmed",
    "guests": {
      "adults": 2,
      "children": 1,
      "infants": 0,
      "pets": 0
    },
    "pricing": {
      "currency": "USD",
      "subtotal": 1275,
      "cleaningFee": 100,
      "serviceFee": 175,
      "taxes": 156,
      "total": 1706,
      "paidAmount": 1706
    },
    "paymentStatus": "paid",
    "createdAt": "2025-05-07T14:30:00Z",
    "confirmedAt": "2025-05-07T14:45:00Z"
  }
}
```

### Cancel Booking

Cancels an existing booking.

**Endpoint:** `POST /bookings/{bookingId}/cancel`

**Auth Required:** Yes (Booking guest or listing host)

**Request Body:**
```json
{
  "reason": "change_of_plans",
  "comments": "My travel plans have changed unexpectedly."
}
```

**Validation Rules:**
- Booking Status: Must be "pending", "confirmed", or "paid"
- Cancellation Policy: Applied based on listing's policy and time until check-in

**Response: `200 OK`**
```json
{
  "status": "success",
  "data": {
    "bookingId": "bkg_12345abcde",
    "status": "cancelled",
    "cancellation": {
      "cancelledBy": "guest",
      "cancelledAt": "2025-05-07T16:30:00Z",
      "reason": "change_of_plans",
      "refundAmount": 1535.40,
      "refundPercentage": 90,
      "refundStatus": "processing"
    }
  }
}
```

### Additional Booking Management Endpoints
- `GET /bookings` - List user's bookings
- `PATCH /bookings/{bookingId}/dates` - Modify booking dates
- `POST /bookings/{bookingId}/extend` - Request booking extension
- `POST /bookings/{bookingId}/review` - Add post-stay review

## Other Features (Brief Overview)

### Search and Filtering API
- `GET /search/listings` - Search for listings with filters for location, dates, price range, amenities, etc.
- `GET /search/autocomplete` - Location autocomplete suggestions

### Payment Integration API
- `POST /payments/booking/{bookingId}` - Process payment for booking
- `GET /payments/methods` - Get saved payment methods
- `POST /payments/methods` - Add payment method
- `GET /payments/payouts` - View host payout history

### Reviews and Ratings API
- `POST /reviews` - Create review for completed stay
- `GET /reviews/listing/{listingId}` - Get all reviews for a listing
- `GET /reviews/user/{userId}` - Get all reviews for a user

### Notifications API
- `GET /notifications` - Retrieve user notifications
- `PATCH /notifications/{notificationId}` - Mark notification as read
- `GET /notifications/settings` - Get notification preferences
- `PATCH /notifications/settings` - Update notification preferences

### Admin Dashboard API
- `GET /admin/users` - List and filter users
- `GET /admin/listings` - List and filter listings
- `GET /admin/bookings` - List and filter bookings
- `POST /admin/moderation/listing/{listingId}` - Moderate listing content