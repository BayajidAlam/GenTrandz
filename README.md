## GenTrandz: A Scalable and Modern Multi-Vendor eCommerce Platform
## Table of Contents

- [Project Overview](#project-overview)
- [System Design](#system-design)
- [Data Model Design](#data-mode-design)
- [Architecture Overview](#architecture-overview)
- [Technology Stack](#technology-stack)
- [User Interface](#user-interface)

## Project Overview

Welcome to GenTrandz, an innovative multi-vendor eCommerce platform designed to simplify business and management operations. I have successfully developed the MVP (Minimum Viable Product), which covers the core functionalities required for our initial launch. Currently, I am focusing on extending the platform by developing advanced features for the Admin and Super Admin roles to enhance control and scalability


## System Design

The system should meet the following requirements:

### Functional requirements

I will design the system for five diffrent role Customer, Sells Manager, Seller, Admin, Super Admin

**Customers**

- Buy, wishlist, or report product
- Checkout cart
- View,search and filter products
- Make and Track Order
- Read blogs
- Contact with supports or chat with sales manager
- Take the help of map to navigate towards shop

**Sells Manager**

- Create Product
- Edit product(availability and others)
- Accept order
- Update tracking
- Chat with customer

**Seller**

- Assign or create, remove managers to his shop and products
- Remove reported item
- Create discount, Offer
- Create Product
- Edit product(availability and others)
- Accept order
- Update tracking
- Chat with sells Managers

**Admin**

- Verify and edit,ban seller
- Add, change the content of the application
- Manage the Sells and Seller of the software
- Business supervision
- Assist seller and sales manager to get a better user experience
- Collect feedback from seller and Sells manager

**Super Admin**

- Add, remove a admin
- Business supervision

### Non-Functional requirements

- High reliability.
- High availability with minimal latency.
- The system should be scalable and efficient

### Extended requirements

- Customers can rate the product, shop after it's completed.
- Payment processing.
- Metrics and analytics

## Estimation and Constraints

Let's start with the estimation and constraints.

### Traffic

Let us assume we have 10000 daily active users (DAU).If on average each user performs 10 actions (such as request a check available product, addtoCart, checkout, etc.) we will have to handle 100000 requests daily.

$$
10000 \times 10 \space actions = 100000 \space /day
$$

**What would be Requests Per Second (RPS) for our system?**

100000 requests per day translate into 1.2 requests per second.

$$
\frac{100000 \space}{(24 \space hrs \times 3600 \space seconds)} = \sim 1.2 \space requests/second
$$

### Storage

If we assume each user comsume on average is 400 bytes, we will require about 0.04 GB of database storage every day.

$$
100000 \space \times 400 \space bytes = \sim 0.04 \space GB/day
$$

And for 10 years, we will require about 146 GB of storage.

$$
0.04 \space GB \times 10 \space years \times 365 \space days = \sim 146 \space GB
$$

### Bandwidth

As our system is handling 0.04 GB of ingress every day, we will require a minimum bandwidth of around 0.46 KB per second.

$$
\frac{0.04 \space GB}{(24 \space hrs \times 3600 \space seconds)} = \sim 0.46 \space KB/second
$$

### High-level estimate

Here is our high-level estimate:

| Type                      | Estimate    |
| ------------------------- | ----------- |
| Daily active users (DAU)  | 10000       |
| Requests per second (RPS) | 1.2/s      |
| Storage (per day)         | ~0.04 GB    |
| Storage (10 years)        | ~146 GB     |
| Bandwidth                 | ~0.46 KB/s |




## Data Model Design

This is the general data model which reflects our requirements.

![matt-mons-datamodel](https://i.ibb.co/cXHJ2Zv/Matt-mons-drawio.png)

We have the following tables:

**User**  
This is the user table that includes all user data with fields such as `id`, `email`, `password`, `role`, `createdAt`, `updatedAt`.

**SuperAdmin**  
This table contains data for super admins, including `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `address`, `profileImg`, `nidNumber`, `userId`, `isActive`, `createdAt`, `updatedAt`.

**Admin**  
This table stores admin data, including fields like `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `address`, `profileImg`, `userId`, `nidNumber`, `isActive`, `createdAt`, `updatedAt`.

**Seller**  
This table holds seller data with fields such as `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `address`, `profileImg`, `userId`, `nidNumber`, `isActive`, `createdAt`, `updatedAt`.

**SellsManager**  
This table stores data for sales managers with fields like `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `address`, `profileImg`, `userId`, `nidNumber`, `isActive`, `shopId`, `createdAt`, `updatedAt`.

**Customer**  
This table holds customer information, including fields like `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `address`, `profileImg`, `userId`, `isActive`, `dob`, `createdAt`, `updatedAt`.

**Conversation**  
This table contains conversation data, including `id`, `message`, `participants`, `senderId`, `receiverId`, `createdAt`, `updatedAt`.

**Message**  
This table holds message data within conversations with fields such as `id`, `message`, `senderId`, `receiverId`, `conversationId`, `createdAt`, `updatedAt`.

**Shop**  
This table contains information about shops, including `id`, `sellerId`, `shopName`, `shopImage`, `rating`, `location`, `isVerified`, `createdAt`, `updatedAt`.

**Coupon**  
This table stores coupon data related to shops, including `id`, `shopId`, `couponName`, `discount`, `shippingCharge`, `validTill`, `createdBy`, `createdAt`, `updatedAt`.

**Product**  
This table contains product data for each shop, including `id`, `shopId`, `productName`, `productMainImage`, `productAdditionalImages`, `shortDescription`, `productDetails`, `minPrice`, `discountPrice`, `discountPercentage`, `moneySaved`, `sellCount`, `createdBy`, `categoryId`, `productSkuId`, `productTags`, `createdAt`, `updatedAt`, `orderId`, `orderOrderId`, `cartId`.

**Category**  
This table holds product categories with fields like `id`, `image`, `title`, `createdAt`, `updatedAt`.

**ProductSku**  
This table contains SKU (Stock Keeping Unit) information for products with fields like `id`, `title`, `quantity`, `availableColor`, `availableSize`, `shopId`, `createdAt`, `updatedAt`.

**Color**  
This table contains color options for products with fields such as `id`, `title`, `shopId`, `createdAt`, `updatedAt`.

**Size**  
This table stores size options for products with fields such as `id`, `title`, `shopId`, `createdAt`, `updatedAt`.

**ProductTags**  
This table holds tags related to products, including `id`, `title`, `createdAt`, `updatedAt`.

**Cart**  
This table stores data related to customer shopping carts with fields such as `id`, `userId`, `productId`, `quantity`, `createdAt`, `updatedAt`.

**Order**  
This table stores order data, including `id`, `fullName`, `contactNumber`, `emergencyContactNumber`, `email`, `address`, `subTotal`, `shippingCharge`, `tax`, `total`, `paidAmount`, `dueAmount`, `orderStatus`, `trnsId`, `isPaid`, `orderPlacedAt`, `payment_acceptedAt`, `delivered_to_curier`, `curier_wareshouse`, `being_delivered`, `delivered`, `canceledAt`, `userId`, `shopId`, `couponId`, `createdAt`, `updatedAt`.

**OrderItem**  
This table stores the details of items in an order, including `id`, `orderId`, `productId`, `quantity`, `createdAt`.

**Payment**  
This table holds payment information for orders, including `id`, `trnxId`, `userId`, `orderId`, `createdAt`, `updatedAt`.

**WishList**  
This table stores customer wish list items, including `id`, `userId`, `productId`, `createdAt`, `updatedAt`.

**ReviewAndRatings**  
This table holds product reviews and ratings from customers, including `id`, `customerId`, `productId`, `comment`, `rating`, `like`, `disLike`, `createdAt`, `updatedAt`.



### What kind of database should we use?

We are developing a monolithic application, and we will use a single [PostgreSQL](https://www.postgresql.org) database to store all of our data. While our data model is quite relational, keeping everything in one database is suitable for our current architecture. This allows us to leverage PostgreSQL's powerful relational capabilities, including complex queries, transactions, and referential integrity, which are critical for our application.

Despite using a monolithic architecture, we can still ensure scalability and maintainability by organizing the database schema in a way that logically separates concerns. We will define clear ownership of tables and ensure that the application components interact with the database in a modular and efficient way. This approach enables us to take full advantage of PostgreSQL's features while avoiding the complexity of managing multiple databases.

## High-level design

Now let us do a high-level design of our system.

We will be using Monolith architecure while we are building the MVP. According to demand we will scale it horizontally and If it reach to limitations we will consider to build to microservice.

### How is the service expected to work?

Here's how our service is expected to work:

- **Customer** places an order by selecting products, adding them to the cart, choosing a payment method, and proceeding to checkout. They can also wishlist products, search, filter items, track orders, and contact support.
- **Seller** manages their shop by creating or editing products, assigning managers, removing reported items, and offering discounts. They accept orders, update tracking, and communicate with the sales manager.
- **Sells Manager** creates and edits products, accepts orders, and tracks shipments. They also chat with customers and assist sellers.
- **Admin** supervises the business, verifies or bans sellers, manages content, and ensures a smooth experience for sellers and sales managers.
- **Super Admin** manages admins, oversees the entire system, and ensures business operations run smoothly.
### Payments

Handling payments at scale is challenging, to simplify our system we can use a third-party payment processor like [Stripe](https://stripe.com) or [SSLCommerz](https://www.paypal.com). Once the payment is complete, the payment processor will redirect the user back to our application and we can set up a [webhook](https://en.wikipedia.org/wiki/Webhook) to capture all the payment-related data.

### Notifications

We will send notifications through our server in future.

## Identify and resolve bottlenecks

Let us identify and resolve bottlenecks such as single points of failure in our design:

- "What if one of our services crashes?"
- "How can we reduce the load on our database?"

To make our system more resilient we can do the following:
- Implement load balancer to distribute traffic across multiple instances or containers
- Use a queue system (e.g., RabbitMQ or Kafka) to offload heavy tasks like email sending, Invoice generation etc.
- Implement logging to identify the problem
- Using caching to reduce load on our database.




## Architecture Overview




## Technology Stack
- **Frontend**: Typescript, Next.js, Redux, Socket.io, Framer Motion, Email.js, React quill, Axios, Tailwind, Ant Design, etc
- **Backend**: Typescript, Node.js, Express.js, Docker, Nginx, PostgreSQL, Prisma, Redis, Stripe, Socket.io, Jest, bcrypt, JWT, etc

- **Containerization**: 
  - **Docker** for creating, managing, and deploying containers.
  - **Docker Hub** for publishing and accessing the container images.

## User Interface
