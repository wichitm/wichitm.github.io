# GameNation API Documentation

> **Version:** 1.0  
> **Base URL:** `https://api.gamenation.com`  
> **Authentication:** OAuth2 Bearer Token (Laravel Passport)

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [API Response Format](#api-response-format)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [API Endpoints](#api-endpoints)
  - [Authentication](#authentication-endpoints)
  - [Catalog Management](#catalog-management)
  - [Order Management](#order-management)
  - [User Management](#user-management)
  - [Wallet System](#wallet-system)
  - [Mission System](#mission-system)
  - [Coupon System](#coupon-system)
  - [OTP System](#otp-system)
  - [Banner Management](#banner-management)
  - [Reporting](#reporting)
  - [Level System](#level-system)
  - [Referral System](#referral-system)
  - [Promotion System](#promotion-system)
  - [Settings](#settings)
  - [Service Integration](#service-integration)
  - [Notifications](#notifications)
  - [Authorization](#authorization)
  - [CNS MetaPay Integration](#cns-metapay-integration)
- [Enums](#enums)
- [Data Models](#data-models)

---

## Overview

GameNation API is a comprehensive gaming and e-commerce platform built with Laravel Lumen 8, designed for the Thai market with extensive telecom integration (particularly DTAC). The API follows a container-based architecture with clear separation of concerns.

### Key Features

- **Gaming & E-commerce Platform** - Product catalog, purchases, payments
- **Gamification System** - Levels, missions, rewards, referrals
- **Payment Processing** - QR payments, multiple payment types, coupons
- **Third-party Integrations** - DTAC, UniPin, CodaShop publishers
- **User Management** - Registration, profiles, KYC, blocking/unblocking
- **Comprehensive Reporting** - Sales and usage analytics

### API Versions

- **v1** - Primary API version with full functionality
- **v2** - Enhanced version of core features with improvements
- **cns** - CNS (third-party integration) specific endpoints

---

## Authentication

The API uses **OAuth2 with Laravel Passport** for authentication. Most endpoints require a valid Bearer token.

### Obtaining Access Token

Use the login endpoint to obtain an access token:

```http
POST /v1/authentication/login
```

### Using Access Token

Include the token in the Authorization header:

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

---

## API Response Format

All API responses follow a consistent JSON format:

### Success Response

```json
{
  "code": 200,
  "message": "Success message",
  "data": {
    // Response data
  }
}
```

### Paginated Response

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    // Array of items
  ],
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 10,
    "per_page": 20,
    "to": 20,
    "total": 200
  }
}
```

---

## Error Handling

### Error Response Format

```json
{
  "code": 422,
  "message": "Invalid input",
  "errors": {
    "field_name": [
      "Error message for this field"
    ]
  }
}
```

### Common HTTP Status Codes

- `200` - Success
- `401` - Unauthorized (invalid/missing token)
- `403` - Forbidden (insufficient permissions)
- `404` - Not Found
- `422` - Validation Error
- `429` - Too Many Requests (rate limited)
- `500` - Internal Server Error

---

## Rate Limiting

Rate limiting is applied to sensitive endpoints, particularly OTP requests:

- **OTP Request**: 5 requests per minute per IP
- **General API**: 60 requests per minute per user

Rate limit headers are included in responses:
- `X-RateLimit-Limit`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

---

# API Endpoints

## Authentication Endpoints

### Login

Authenticate a user and obtain access token.

```http
POST /v1/authentication/login
```

**Request Body:**
```json
{
  "username": "john.doe@example.com",
  "password": "abc123"
}
```

**Parameters:**
- `username` (string, required) - Email or phone number
- `password` (string, required) - User password

**Response:**
```json
{
  "code": 200,
  "message": "Login successful",
  "data": {
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9...",
    "token_type": "Bearer",
    "expires_at": "2024-01-01 10:30:00",
    "user": {
      "id": 1,
      "username": "johndoe",
      "email": "john.doe@example.com",
      "phone_number": "66123456789"
    }
  }
}
```

### Register

Register a new user account.

```http
POST /v1/authentication/register
```

**Request Body:**
```json
{
  "username": "johndoe",
  "email": "john.doe@example.com",
  "phone_no": "0123456789",
  "password": "abc123456",
  "password_confirmation": "abc123456",
  "otp_ref": "51231235123",
  "referral_code": "qjh219xj",
  "agree_term": true,
  "token": "Xej32a0SHxnmg3gjk2386xzn1"
}
```

**Parameters:**
- `username` (string, required) - Alphanumeric username, max 125 chars, unique
- `email` (string, required) - Valid email address, max 125 chars, unique
- `phone_no` (string, required) - Phone number, 5-15 chars, unique
- `password` (string, required) - Password, 8-12 chars, alphanumeric
- `password_confirmation` (string, required) - Password confirmation
- `otp_ref` (string, required) - OTP reference for phone verification
- `referral_code` (string, optional) - Referral code from existing user
- `agree_term` (boolean, required) - Terms and conditions agreement
- `token` (string, required) - reCAPTCHA token

### Reset Password

Reset user password with email verification.

```http
POST /v1/authentication/reset-password
```

### Refresh Token

Refresh access token.

```http
POST /v1/authentication/refresh-token
```

### Logout

Logout and invalidate access token. **(Requires Authentication)**

```http
POST /v1/authentication/logout
```

---

## Catalog Management

### List Products

Get list of products with filtering options.

```http
GET /v1/catalog/products
```

**Query Parameters:**
- `catalog_id` (integer, optional) - Filter by catalog ID
- `catalog_slug` (string, optional) - Filter by catalog slug
- `distributor` (integer, optional) - Filter by distributor type
- `type` (integer, optional) - Filter by product type
- `variant_type` (integer, optional) - Filter by variant type
- `name` (string, optional) - Search by product name
- `genre_ids` (string, optional) - Comma-separated genre IDs
- `max_price` (number, optional) - Maximum price filter
- `status` (integer, optional) - Filter by status (0=inactive, 1=active)
- `sort` (string, optional) - Sort by field,direction (e.g., "name,asc")
- `page` (integer, optional) - Page number for pagination

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "name": "Mobile Legends",
      "slug": "mobile-legends",
      "description": "Popular MOBA game...",
      "keywords": "mobilelegends, mmorpg, action",
      "icon_path_url": {
        "thumbnail": "https://cdn.gamenation.com/icons/ml_thumb.jpg",
        "optimize": "https://cdn.gamenation.com/icons/ml_opt.jpg"
      },
      "icon_alt_text": "Mobile Legends icon",
      "variant_type_description": "Topup",
      "usable_payment_type": [0, 1],
      "sales_tag": "Hot Deal",
      "product_genres": [
        {
          "id": 1,
          "name": "Action",
          "slug": "action"
        }
      ],
      "product_variants": [
        {
          "id": 1,
          "name": "50 Diamonds",
          "sku_price": 10.00,
          "discount_price": 8.00
        }
      ],
      "publisher": {
        "id": 1,
        "name": "UniPin"
      },
      "max_discount_percent": 20
    }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 150
  }
}
```

### Get Product Details

Get detailed information about a specific product.

```http
GET /v1/catalog/products/{productSearchKey}
```

**Parameters:**
- `productSearchKey` (string) - Product ID or slug

### Create Product

Create a new product. **(Requires Authentication & Admin Permission)**

```http
POST /v1/catalog/products
```

**Request Body:**
```json
{
  "sort_order": 1,
  "type": 1,
  "variant_type": 1,
  "distributor": 1,
  "name": "Mobile Legends",
  "game_code": "AOVV_ID",
  "description": "Rich text content...",
  "keywords": "mobilelegends, mmorpg, action",
  "purchase_with_coupon_only": 1,
  "usable_payment_type": [0, 1],
  "status": 1,
  "icon": "iVBORw0KGgoAAAA...",
  "icon_alt_text": "Mobile Legends icon",
  "tags": [1, 2],
  "genres": [1, 2],
  "publisher": 1,
  "sales_tag": "Hot Deal"
}
```

### List Catalogs

Get list of all catalogs.

```http
GET /v1/catalog/
```

### List Genres

Get list of all product genres.

```http
GET /v1/catalog/genres/
```

### List Tags

Get list of all product tags.

```http
GET /v1/catalog/tags/
```

---

## Order Management

### Create Purchase

Initiate a product purchase. **(Requires Authentication)**

```http
POST /v1/order/purchase
```

**Request Body:**
```json
{
  "product_variant_id": 1
}
```

**Parameters:**
- `product_variant_id` (integer, required) - ID of the product variant to purchase

**Response:**
```json
{
  "code": 200,
  "message": "Purchase created successfully",
  "data": {
    "user_purchase_id": 1,
    "reference_no": "GN20231201001",
    "product": {
      "id": 1,
      "name": "Mobile Legends",
      "variant_name": "50 Diamonds"
    },
    "total_amount": 10.00,
    "status": 0
  }
}
```

### Confirm Purchase

Confirm and process a purchase with OTP verification. **(Requires Authentication)**

```http
POST /v1/order/purchase/confirm
```

**Request Body:**
```json
{
  "user_purchase_id": 1,
  "otp_ref": "5234021",
  "additional_param": {}
}
```

### List User Purchases

Get list of user's purchase history. **(Requires Authentication)**

```http
GET /v1/order/user-purchase/
```

**Query Parameters:**
- `status` (integer, optional) - Filter by purchase status
- `date_from` (string, optional) - Start date filter (YYYY-MM-DD)
- `date_to` (string, optional) - End date filter (YYYY-MM-DD)
- `page` (integer, optional) - Page number

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "product": {
        "id": 1,
        "name": "Mobile Legends",
        "slug": "mobile-legends",
        "image_url": "https://cdn.gamenation.com/products/ml.jpg",
        "variant_type_description": "Topup",
        "distributor_description": "UniPin"
      },
      "product_variant": {
        "id": 1,
        "name": "50 Diamonds",
        "sku_price": 10.00
      },
      "reference_no": "GN20231201001",
      "tx_id": "TXN123456789",
      "payment_type": 0,
      "payment_type_description": "DTAC",
      "subtotal_amount": 10.00,
      "discount_amount": 2.00,
      "total_amount": 8.00,
      "total_coin_redeem": 50,
      "total_reward_point": 375,
      "coupon_code": "HOTDEALS",
      "redeem_code": "ABC123DEF456",
      "status": 2,
      "created_at": "2023-12-01 10:30:00"
    }
  ]
}
```

### Get Purchase Details

Get detailed information about a specific purchase. **(Requires Authentication)**

```http
GET /v1/order/user-purchase/{userPurchaseId}
```

### Update Purchase Attributes

Update custom attributes for a purchase. **(Requires Authentication)**

```http
PUT /v1/order/purchase/update-attribute/{userPurchaseId}
```

**Request Body:**
```json
{
  "custom_attribute": [
    {
      "product_attribute_id": 1,
      "value": "152529"
    }
  ],
  "default": 1,
  "existing": false
}
```

### Process Refund

Process refund for a purchase. **(Requires Authentication & Admin Permission)**

```http
PUT /v1/order/purchase/refund/{userPurchaseId}
```

---

## User Management

### Get User Profile

Get current user's profile information. **(Requires Authentication)**

```http
GET /v1/user/me
```

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "id": 1,
    "referral_code": "ABC123XYZ",
    "username": "johndoe",
    "email": "j***@example.com",
    "phone_number": "66123456789",
    "display_phone_number": "0123456789",
    "pay_type": 0,
    "pay_type_description": "Prepaid",
    "avatar_path_url": {
      "blur": "https://cdn.gamenation.com/avatars/user_1_blur.jpg",
      "thumbnail": "https://cdn.gamenation.com/avatars/user_1_thumb.jpg",
      "optimize": "https://cdn.gamenation.com/avatars/user_1_opt.jpg"
    },
    "wallets": [
      {
        "name": "GN Coin",
        "slug": "gn-coin",
        "balance_amount": 1500.00,
        "used_amount": 500.00,
        "accumulated_amount": 2000.00
      }
    ],
    "email_status": 1,
    "email_status_description": "Verified",
    "kyc_status": 1,
    "kyc_status_description": "Active",
    "is_dtac_status": 1,
    "is_dtac_status_description": "Yes",
    "consent_status": 1,
    "consent_status_description": "Consented",
    "purchase": {
      "daily_limit": 1000.00,
      "monthly_limit": 10000.00,
      "daily_used": 150.00,
      "monthly_used": 1200.00
    },
    "credit_balance": 25.50,
    "level": {
      "id": 2,
      "name": "Silver",
      "tier": 2,
      "total_spending": 2500.00,
      "target_level": "Gold",
      "target_amount": 5000.00,
      "percent_progress": 50,
      "is_max_level": false
    }
  }
}
```

### Change Password

Change user password. **(Requires Authentication)**

```http
PUT /v1/user/profile/change-password
```

**Request Body:**
```json
{
  "current_password": "oldpassword123",
  "password": "newpassword123",
  "password_confirmation": "newpassword123"
}
```

### Change Email

Change user email address. **(Requires Authentication)**

```http
PUT /v1/user/profile/change-email
```

### Upload Avatar

Upload user avatar image. **(Requires Authentication)**

```http
POST /v1/user/profile/upload-avatar
```

**Request Body (multipart/form-data):**
- `avatar` (file, required) - Image file (JPEG, PNG, max 2MB)

---

## Wallet System

### Get Wallet Types

Get list of available wallet types. **(Requires Authentication)**

```http
GET /v1/wallet/wallet-type/
```

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "name": "GN Coin",
      "slug": "gn-coin",
      "description": "GameNation virtual currency",
      "status": 1
    }
  ]
}
```

---

## Mission System

### List User Missions

Get list of available missions for the user. **(Requires Authentication)**

```http
GET /v1/mission/users/general
```

### Claim Mission Reward

Claim reward for completed mission. **(Requires Authentication)**

```http
PUT /v1/mission/claim/{missionId}
```

### List Gifts

Get list of available gifts. **(Requires Authentication)**

```http
GET /v1/mission/gifts/
```

---

## Coupon System

### List Coupons

Get list of available coupons. **(Requires Authentication)**

```http
GET /v1/coupon/
```

### Create Coupon

Create a new coupon. **(Requires Authentication & Admin Permission)**

```http
POST /v1/coupon/
```

### Validate Coupon

Validate coupon code for purchase. **(Requires Authentication)**

```http
POST /v1/order/purchase/validate-coupon
```

**Request Body:**
```json
{
  "coupon_code": "HOTDEALS",
  "product_variant_id": 1
}
```

---

## OTP System

### Request OTP

Request OTP for phone verification. **(Rate Limited: 5/minute)**

```http
POST /v1/otp/request
```

**Request Body:**
```json
{
  "phone_no": "0123456789",
  "type": 1
}
```

**Parameters:**
- `phone_no` (string, required) - Phone number
- `type` (integer, required) - OTP type (1=registration, 2=purchase, etc.)

### Verify OTP

Verify OTP code.

```http
POST /v1/otp/verify
```

**Request Body:**
```json
{
  "phone_no": "0123456789",
  "otp_code": "123456",
  "otp_ref": "5234021"
}
```

---

## Banner Management

### List Banners

Get list of active banners.

```http
GET /v1/banner/
```

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "title": "Hot Deals",
      "description": "Special promotion for Mobile Legends",
      "image_url": "https://cdn.gamenation.com/banners/hotdeals.jpg",
      "link_url": "https://gamenation.com/promotions/hotdeals",
      "sort_order": 1,
      "status": 1,
      "start_date": "2023-12-01",
      "end_date": "2023-12-31"
    }
  ]
}
```

---

## Reporting

### Get Report Summary

Get summary report data. **(Requires Authentication & Admin Permission)**

```http
GET /v1/report/summary
```

### Get Top Selling Products

Get top selling products report. **(Requires Authentication & Admin Permission)**

```http
GET /v1/report/top-selling-product
```

**Query Parameters:**
- `date_from` (string, optional) - Start date (YYYY-MM-DD)
- `date_to` (string, optional) - End date (YYYY-MM-DD)
- `limit` (integer, optional) - Number of results (default: 10)

---

## Level System

### List Levels

Get list of all user levels. **(Requires Authentication)**

```http
GET /v1/level/
```

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "name": "Bronze",
      "tier": 1,
      "minimum_spending": 0.00,
      "maximum_spending": 1000.00,
      "loyalty_rate": 5.0,
      "description": "Entry level membership"
    }
  ]
}
```

---

## Settings

### Get Maintenance Status

Check if the application is under maintenance.

```http
GET /v1/setting/app/maintenance-status
```

**Response:**
```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "is_maintenance": false,
    "maintenance_message": null
  }
}
```

---

## CNS MetaPay Integration

### List Games

Get list of available games in CNS MetaPay. **(Requires Authentication)**

```http
GET /cns/meta-pay/games/
```

### Get Game Details

Get detailed information about a specific game. **(Requires Authentication)**

```http
GET /cns/meta-pay/games/{code}
```

### Place Game Order

Place an order for game items. **(Requires Authentication)**

```http
POST /cns/meta-pay/games/{code}/place-order
```

---

# Enums

## Payment Types
- `0` - DTAC
- `1` - QR Payment  
- `2` - True Wallet

## General Status
- `0` - Inactive
- `1` - Active

## User Purchase Status
- `0` - Pending
- `1` - Processing
- `2` - Completed
- `3` - Failed
- `4` - Cancelled
- `5` - Refunded

## Product Types
- `1` - Topup
- `2` - Voucher
- `3` - Physical Item

## Variant Types
- `1` - Digital
- `2` - Physical

## User Pay Types
- `0` - Prepaid
- `1` - Postpaid

---

# Data Models

## User Model
```json
{
  "id": "integer",
  "username": "string",
  "email": "string", 
  "phone_number": "string",
  "referral_code": "string",
  "avatar_path": "string",
  "email_status": "integer",
  "kyc_status": "integer",
  "pay_type": "integer",
  "is_dtac_status": "integer",
  "consent_status": "integer",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

## Product Model
```json
{
  "id": "integer",
  "name": "string",
  "slug": "string",
  "description": "text",
  "keywords": "string",
  "icon_path": "string",
  "icon_alt_text": "string",
  "type": "integer",
  "variant_type": "integer",
  "distributor": "integer",
  "status": "integer",
  "sort_order": "integer",
  "sales_tag": "string",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

## Purchase Model
```json
{
  "id": "integer",
  "user_id": "integer",
  "product_id": "integer",
  "product_variant_id": "integer",
  "reference_no": "string",
  "tx_id": "string",
  "payment_type": "integer",
  "subtotal_amount": "decimal",
  "discount_amount": "decimal",
  "total_amount": "decimal",
  "coupon_code": "string",
  "redeem_code": "string",
  "status": "integer",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

---

## Support & Contact

For API support and technical questions:
- **Email**: api-support@gamenation.com
- **Documentation**: https://docs.gamenation.com
- **Status Page**: https://status.gamenation.com

---

**Last Updated**: December 2023  
**API Version**: 1.0
