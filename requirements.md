# Airbnb Clone Backend - Requirement Specifications

## 1. User Authentication

### Feature Description
Handles user registration, login, and profile management for guests, hosts, and admins.

### API Endpoints
- **POST /users/register** – Register a new user  
- **POST /users/login** – Authenticate a user and issue a JWT  
- **GET /users/{user_id}** – Retrieve user profile  
- **PUT /users/{user_id}** – Update user profile  
- **DELETE /users/{user_id}** – Delete user account (admin only)

### Input Specifications
- `first_name`: string, required, max 100 characters  
- `last_name`: string, required, max 100 characters  
- `email`: string, required, valid email format, unique  
- `password`: string, required, min 8 characters, must include letters and numbers  
- `role`: enum [guest, host, admin], default guest  

### Output Specifications
- Success responses return JSON objects with user details (excluding password) and a JWT for login  
- Error responses return JSON with error messages and HTTP status codes (e.g., 400 Bad Request, 401 Unauthorized)

### Validation Rules
- Email uniqueness and format validation  
- Password strength enforcement  
- JWT token validation for protected routes  
- Role-based access control (RBAC)

### Performance Criteria
- Registration and login requests should respond within 200ms under normal load  
- API must handle at least 100 concurrent authentication requests

---

## 2. Property Management

### Feature Description
Enables hosts to create, update, view, and delete property listings.

### API Endpoints
- **POST /properties/** – Add a new property  
- **GET /properties/** – List all properties with optional filters (location, price, amenities)  
- **GET /properties/{property_id}** – Retrieve details of a single property  
- **PUT /properties/{property_id}** – Update property details  
- **DELETE /properties/{property_id}** – Delete a property listing

### Input Specifications
- `name`: string, required, max 150 characters  
- `description`: string, required  
- `address`: string, required  
- `city`: string, required  
- `country`: string, required  
- `pricepernight`: decimal, required, >= 0  
- `amenities`: array of strings, optional  
- `availability`: date range, optional  

### Output Specifications
- Success responses return JSON objects with property details  
- Error responses return JSON with status codes (400, 404, 403)

### Validation Rules
- Property must belong to a valid host  
- Price must be non-negative  
- Required fields cannot be empty  
- Only the host who created the property or admin can update/delete

### Performance Criteria
- Property listing and search queries should respond within 500ms for 1000+ listings  
- Supports pagination for large result sets

---

## 3. Booking System

### Feature Description
Handles property reservations, cancellations, and booking status tracking.

### API Endpoints
- **POST /bookings/** – Create a new booking  
- **GET /bookings/** – List all bookings for a user  
- **GET /bookings/{booking_id}** – Retrieve booking details  
- **PUT /bookings/{booking_id}** – Update booking (e.g., cancel)  
- **DELETE /bookings/{booking_id}** – Delete a booking (admin only)

### Input Specifications
- `property_id`: UUID, required  
- `user_id`: UUID, required  
- `start_date`: date, required, must be before `end_date`  
- `end_date`: date, required  
- `status`: enum [pending, confirmed, cancelled], default pending

### Output Specifications
- Success responses return JSON with booking details including total price  
- Error responses include HTTP status codes like 400, 404, 409 (conflict for double booking)

### Validation Rules
- Ensure property is available for selected dates  
- Prevent overlapping bookings for the same property  
- Users can only cancel their own bookings  
- Admins can override bookings if necessary

### Performance Criteria
- Booking creation should complete within 300ms under normal load  
- System should handle at least 500 concurrent booking requests without failure

---

## Notes
- All APIs must include proper error handling and return meaningful messages  
- All sensitive data such as passwords and payment info must be encrypted  
- JWT-based authentication required for all protected routes  
- Database indexing should be implemented for frequently queried fields like `property_id`, `user_id`, and `city`
