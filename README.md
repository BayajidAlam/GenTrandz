Project Todos

- [x] Requirement analysis
- [x] Selecting tech stack
- [x] System design
- [x] Api end points
- [ ] Development (Ongoing)
- [x] Deployment
- [ ] Api Documentation
- [x] Browser caching and cache-control
- [ ] Rate limiting
- [x] Write test case
- [ ] Complete documentation


## Table of Contents

- [Project Overview](#project-overview)
- [Technology Stack](#technology-stack)
- [System Design](#system-design)
- [Getting Started](#getting-started)
- [Folder Structure](#folder-structure)
- [Usage](#usage)

## Project Overview

Welcome to the frontend repository of matt-mons, the innovative multi vendor ecommerce Management System designed to streamline your ecommerce management experience. Firstly I will go with MVP which will meet the minimum requirements to run our initial business, which means I am prioritising the user fetching feature and the manager roll feature that will be developed first. Here I am planning version(v1) requirements.

This frontend repository works in conjunction with the matt-mons backend, providing a user-friendly interface for managing vehicles efficiently.

## Technology Stack

- **Next.js:** React framework for building user interfaces.
- **Ant Design:** UI library for React components.
- **Tailwind CSS:** Utility-first CSS framework.
- **Redux:** State management for React applications.
- **Socket.io:** Real-time bidirectional event-based communication.
- **Framer Motion:** Animation library for React components.
- **EmailJS:** Service for sending emails directly from client-side JavaScript.

## System Design

The system should meet the following requirements:

### Functional requirements

I will design the system for five diffrent role Customer, Sells Manager, Seller, Admin, Super Admin

**Customers**

- Buy, wishlist, or report product
- Checkout cart
- Take AI guidance to shop (v2)
- View,search and filter products
- Make and Track Order
- Read blogs
- Contact with supports or chat with sales manager(V2)
- Take the help of map to navigate towards shop(V2)

**Sells Manager**

- Create Product
- Edit product(availability and others)
- Accept order
- Update tracking
- Chat with customer(V2)

**Seller**

- Assign or create, remove managers to his shop and products
- Remove reported item
- Create discount, Offer
- Create Product
- Edit product(availability and others)
- Accept order
- Update tracking
- Chat with customer(V2)

**Admin**

- Verify and edit,ban seller
- Add, change the content of the application
- Manage the Sells and Seller of the software
- Business supervision
- Assist seller and sales manager to get a better user experience
- Collect feedback from seller and Sells manager

**Admin**

- Add, remove a admin
- Business supervision

### Non-Functional requirements

- High reliability.
- High availability with minimal latency.
- The system should be scalable and efficient(V2)

### Extended requirements

- Customers can rate the product, shop after it's completed.
- Payment processing.
- Metrics and analytics(V2).

## Estimation and Constraints

Let's start with the estimation and constraints.

### Traffic

Let us assume we have 100 million daily active users (DAU) with 1 million drivers and on average our platform enables 10 million rides daily.
Let us assume we have 10000 active users(DAU)
If on average each user performs 10 actions (such as request a check available product, addtoCart, checkout, etc.) we will have to handle 100000 requests daily.

$$
10000 \times 10 \space actions = 100000 \space /day
$$

**What would be Requests Per Second (RPS) for our system?**

100000 requests per day translate into 1.15 requests per second.

$$
\frac{100000 \space}{(24 \space hrs \times 3600 \space seconds)} = \sim 1.15 \space requests/second
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

As our system is handling 0.04 GB of ingress every day, we will require a minimum bandwidth of around 0.462 KB per second.

$$
\frac{0.04 \space GB}{(24 \space hrs \times 3600 \space seconds)} = \sim 0.462 \space KB/second
$$

### High-level estimate

Here is our high-level estimate:

| Type                      | Estimate    |
| ------------------------- | ----------- |
| Daily active users (DAU)  | 10000       |
| Requests per second (RPS) | 1.15/s      |
| Storage (per day)         | ~0.04 GB    |
| Storage (10 years)        | ~146 GB     |
| Bandwidth                 | ~0.462 KB/s |

## Data model design

This is the general data model which reflects our requirements.

![matt-mons-datamodel](https://i.ibb.co/cXHJ2Zv/Matt-mons-drawio.png)

We have the following tables:

**customers**

This table will contain a customer's information such as `name`, `email`, and other details.

**drivers**

This table will contain a driver's information such as `name`, `email`, `dob` and other details.

**trips**

This table represents the trip taken by the customer and stores data such as `source`, `destination`, and `status` of the trip.

**cabs**

This table stores data such as the registration number, and type (like Uber Go, Uber XL, etc.) of the cab that the driver will be driving.

**ratings**

As the name suggests, this table stores the `rating` and `feedback` for the trip.

**payments**

The payments table contains the payment-related data with the corresponding `tripID`.

### What kind of database should we use?

While our data model seems quite relational, we don't necessarily need to store everything in a single database, as this can limit our scalability and quickly become a bottleneck.

We will split the data between different services each having ownership over a particular table. Then we can use a relational database such as [PostgreSQL](https://www.postgresql.org).

## High-level design

Now let us do a high-level design of our system.

### Architecture

We will be using Monolith architecure while we are building our first version. According to demand we will scale it horizontally and If it reach to limitations we will consider to build to microservice(May be on v3).

### How is the service expected to work?

Here's how our service is expected to work:

1. Customer requests a ride by specifying the source, destination, cab type, payment method, etc.
2. Ride service registers this request, finds nearby drivers, and calculates the estimated time of arrival (ETA).
3. The request is then broadcasted to the nearby drivers for them to accept or deny.
4. If the driver accepts, the customer is notified about the live location of the driver with the estimated time of arrival (ETA) while they wait for pickup.
5. The customer is picked up and the driver can start the trip.
6. Once the destination is reached, the driver will mark the ride as complete and collect payment.
7. After the payment is complete, the customer can leave a rating and feedback for the trip if they like.

### Payments

Handling payments at scale is challenging, to simplify our system we can use a third-party payment processor like [Stripe](https://stripe.com) or [SSLCommerz](https://www.paypal.com). Once the payment is complete, the payment processor will redirect the user back to our application and we can set up a [webhook](https://en.wikipedia.org/wiki/Webhook) to capture all the payment-related data.

### Notifications

We will send notifications through our server(v2)

## Detailed design

It's time to discuss our design decisions in detail.

## Identify and resolve bottlenecks

Let us identify and resolve bottlenecks such as single points of failure in our design:

- "What if one of our services crashes?"
- "How can we reduce the load on our database?"

To make our system more resilient we can do the following:

- Implement logging to identify the problem
- Using caching to reduce load on our database.

## Getting Started

To get started with the ECOM frontend, follow these steps:

1. Clone the repository: `git clone [frontend_repository_url]`
2. Install dependencies: `yarn install`
3. Configure environment variables. (Check env.example)
4. Start the development server: `yarn dev`

## Folder Structure

The frontend codebase follows given modular and organized folder structure:

- `/src`
  - `/app`: Pages of app router.
  - `/assets`: Image, svg and other resources.
  - `/components`: Reusable React components.
  - `/redux`: Redux store setup and actions.
    - `/api`: API requests and integration with the backend.
  - `constants`: Constant vaiable of the project.
  - `helpers`: Helper functions.
  - `hooks`: Necessary hooks.
  - `lib`: Libarary functions.
  - `schemas`: Schemas of the project for validation.
  - `services`: Service like auth service.
  - `types`: Defined common types of the project.
  - `utils`: Utility functions declared here.

## Usage

To interact with the ECOM frontend, follow these steps:

1. **Installation:**

   - Clone the frontend repository: `git clone [frontend_repository_url]`
   - Install dependencies: `yarn install`

2. **Configuration:**

3. **Development Server:**

   - Start the development server: `yarn dev`
   - Open your browser and navigate to `http://localhost:3000` to access the ECOM application in development mode.

4. **User Authentication:**

   - Use the predefined user accounts for testing:
     - **Super Admin:**
       - User ID:
       - Password:
     - **Admin:**
       - User ID:
       - Password:

5. **Explore Features:**

   - Navigate through the dashboard to access different features:

6. **Animation with Framer Motion:**

   - Experience smooth and interactive animations integrated with Framer Motion.

7. **Real-time Communication with Socket.io:**

   - Test the real-time collaborative chat system that fosters seamless communication among team members.

8. **Email Integration with EmailJS:**
   - Explore functionalities that involve email communication directly from the client-side using EmailJS.