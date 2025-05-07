# Airbnb Clone Use Cases

## User Management

### Guest Registration
**Actor:** Unregistered User  
**Description:** A new user registers for an account to book properties.  
**Flow:**
1. User enters personal information (name, email, password)
2. System validates information and creates account
3. User receives confirmation email
4. User can now search and book properties

### Host Registration
**Actor:** User  
**Description:** An existing user registers as a property host.  
**Flow:**
1. User navigates to "Become a Host" section
2. User provides additional required information
3. System updates user role to "host"
4. User can now list properties

## Property Management

### List a Property
**Actor:** Host  
**Description:** Host adds a new property to the platform.  
**Flow:**
1. Host enters property details (name, description, location, price)
2. Host uploads property photos
3. System creates new property listing
4. Property becomes available for booking

### Update Property Details
**Actor:** Host  
**Description:** Host modifies information about their property.  
**Flow:**
1. Host selects property to edit
2. Host updates desired information
3. System saves changes
4. Updated information is displayed to users

## Booking Process

### Search Properties
**Actor:** User  
**Description:** User searches for available properties.  
**Flow:**
1. User enters location, dates, and guest count
2. System displays matching available properties
3. User can filter results by price, amenities, etc.

### Make a Booking
**Actor:** User  
**Description:** User books an available property.  
**Flow:**
1. User selects property and confirms dates
2. System calculates total price
3. User confirms booking request
4. Host receives notification of booking request
5. Host confirms booking
6. User receives booking confirmation

### Cancel a Booking
**Actor:** User or Host  
**Description:** Cancellation of an existing booking.  
**Flow:**
1. User/Host selects booking to cancel
2. System calculates any applicable fees
3. User/Host confirms cancellation
4. Both parties receive cancellation notification
5. Property becomes available for new bookings

## Payment Processing

### Process Payment
**Actor:** User  
**Description:** User pays for confirmed booking.  
**Flow:**
1. User selects payment method
2. System processes payment securely
3. Host and User receive payment confirmation
4. System records transaction details

### Add Payment Method
**Actor:** User  
**Description:** User adds a new payment method to their account.  
**Flow:**
1. User navigates to payment settings
2. User enters payment details
3. System securely stores payment information
4. Payment method available for future bookings

## Reviews and Feedback

### Leave Property Review
**Actor:** User  
**Description:** User reviews a property after their stay.  
**Flow:**
1. User receives prompt after checkout date
2. User rates property and leaves comment
3. System publishes review on property listing
4. Host receives notification of new review

### Reply to Review
**Actor:** Host  
**Description:** Host responds to a user's review.  
**Flow:**
1. Host receives review notification
2. Host writes response
3. System publishes response with the original review

## Communication

### Send Message
**Actor:** User or Host  
**Description:** Communication between users and hosts.  
**Flow:**
1. User/Host selects recipient
2. User/Host writes and sends message
3. Recipient receives notification
4. Message thread is created/updated

### Respond to Inquiry
**Actor:** Host  
**Description:** Host responds to property inquiries.  
**Flow:**
1. Host receives inquiry notification
2. Host reviews inquiry details
3. Host responds to user questions
4. User receives response notification

## Administrative Functions

### Moderate Content
**Actor:** Admin  
**Description:** Admin reviews flagged content.  
**Flow:**
1. Admin reviews flagged listings, reviews, or messages
2. Admin determines appropriate action
3. Admin implements action (approve, remove, warn user)
4. Affected users are notified of action

### Process Disputes
**Actor:** Admin  
**Description:** Admin resolves disputes between users and hosts.  
**Flow:**
1. Admin reviews dispute details from both parties
2. Admin may request additional information
3. Admin makes determination
4. System implements resolution (refund, rebooking, etc.)
5. Both parties are notified of resolution