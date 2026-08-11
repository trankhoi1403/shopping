# Shopping API - DDD Implementation Plan

## Project Vision
Build a shopping API demonstrating Domain-Driven Design principles with a complete order lifecycle: order placement → payment → confirmation → shipment → delivery.

## High-Level Architecture

### Domains (Bounded Contexts)
1. **User Domain**: User creation and profile management
2. **Product Domain**: Product catalog and inventory
3. **Order Domain**: Order creation and state management
4. **Payment Domain**: Payment processing and verification
5. **Shipping Domain**: Shipment tracking and delivery

### Technology Stack
- **Language**: C# with .NET 8
- **API**: ASP.NET Core (REST with Controllers)
- **Database**: SQL Server (Docker)
- **Message Queue**: RabbitMQ (Docker)
- **Architecture Pattern**: Domain-Driven Design with Event-Driven Architecture

### Infrastructure
```
┌─────────────────┐
│   ASP.NET Core  │  (REST API)
│   Controllers   │
└────────┬────────┘
         │
    ┌────┴─────────────────────────────┐
    │                                  │
┌───▼──────────────┐          ┌───────▼──────────┐
│  Domain Layer    │          │  Message Queue   │
│  (Business       │◄────────►│  (RabbitMQ)      │
│   Logic)         │          │  Event Bus       │
└───┬──────────────┘          └──────────────────┘
    │
┌───▼──────────────┐
│  Data Layer      │
│  (EF Core/SQL)   │
└──────────────────┘
    │
    └───────────────────────────────────┐
                                        │
                          ┌─────────────▼────────┐
                          │   SQL Server (DB)    │
                          │   (Docker Container) │
                          └──────────────────────┘
```

## Project Structure

```
shopping/
├── api/
│   ├── Controllers/
│   │   ├── UserController.cs
│   │   ├── ProductController.cs
│   │   ├── OrderController.cs
│   │   └── ...
│   ├── Domains/
│   │   ├── User/
│   │   │   ├── UserAggregate/
│   │   │   ├── UserService.cs
│   │   │   ├── IUserRepository.cs
│   │   │   └── UserDomainEvents.cs
│   │   ├── Product/
│   │   │   ├── ProductAggregate/
│   │   │   ├── ProductService.cs
│   │   │   ├── IProductRepository.cs
│   │   │   └── ProductDomainEvents.cs
│   │   ├── Order/
│   │   │   ├── OrderAggregate/
│   │   │   ├── OrderService.cs
│   │   │   ├── IOrderRepository.cs
│   │   │   └── OrderDomainEvents.cs
│   │   ├── Payment/
│   │   │   ├── PaymentAggregate/
│   │   │   ├── PaymentService.cs
│   │   │   ├── IPaymentRepository.cs
│   │   │   └── PaymentDomainEvents.cs
│   │   └── Shipping/
│   │       ├── ShippingAggregate/
│   │       ├── ShippingService.cs
│   │       ├── IShippingRepository.cs
│   │       └── ShippingDomainEvents.cs
│   ├── Application/
│   │   ├── Commands/
│   │   │   ├── CreateUserCommand.cs
│   │   │   ├── CreateProductCommand.cs
│   │   │   ├── CreateOrderCommand.cs
│   │   │   ├── ProcessPaymentCommand.cs
│   │   │   └── CreateShipmentCommand.cs
│   │   ├── Queries/
│   │   │   ├── GetUserQuery.cs
│   │   │   ├── GetProductQuery.cs
│   │   │   ├── GetOrderQuery.cs
│   │   │   └── ...
│   │   ├── CommandHandlers/
│   │   └── QueryHandlers/
│   ├── Infrastructure/
│   │   ├── Persistence/
│   │   │   ├── ShoppingDbContext.cs
│   │   │   ├── Repositories/
│   │   │   └── EFCore Mappings
│   │   ├── MessageQueue/
│   │   │   ├── RabbitMQConnection.cs
│   │   │   ├── RabbitMQPublisher.cs
│   │   │   ├── EventSubscribers/
│   │   │   └── MessageHandlers/
│   │   └── DependencyInjection.cs
│   ├── Shared/
│   │   ├── Common/
│   │   │   ├── ValueObjects/
│   │   │   ├── AggregateRoot.cs
│   │   │   └── DomainEvent.cs
│   │   ├── DTOs/
│   │   └── Exceptions/
│   ├── Program.cs
│   ├── appsettings.json
│   └── api.csproj
├── docker-compose.yml (SQL + RabbitMQ)
└── plan.md (this file)
```

## Implementation Phases

### Phase 1: Foundation & Setup
**Goal**: Set up basic infrastructure and shared patterns

#### 1.1 Project Structure & Dependencies
- [ ] Create domain folders structure
- [ ] Add NuGet packages:
  - Entity Framework Core (SQL Server provider)
  - RabbitMQ.Client
  - MediatR (for command/query handlers)
  - AutoMapper (for DTOs)

#### 1.2 Core DDD Patterns
- [ ] Create `AggregateRoot` base class
- [ ] Create `DomainEvent` base class
- [ ] Create `ValueObject` base class
- [ ] Create repository interfaces pattern
- [ ] Create exception hierarchy

#### 1.3 Docker Infrastructure
- [ ] Create `docker-compose.yml` with SQL Server & RabbitMQ
- [ ] Update `appsettings.json` with connection strings
- [ ] Test database connectivity

**Expected Outcome**: Runnable project with Docker containers, DDD base classes ready, project structure in place.

---

### Phase 2: User Domain
**Goal**: Implement user creation and management

#### 2.1 User Domain Model
- [ ] Create `User` aggregate root
  - Properties: UserId, UserName, Password (hashed), CreatedAt
  - Methods: Create(), UpdateProfile()
- [ ] Create `UserDomainEvents.cs`
  - `UserCreatedEvent`
- [ ] Create `IUserRepository` interface
- [ ] Create `UserService` domain service

#### 2.2 User Persistence
- [ ] Create `ShoppingDbContext` with User DbSet
- [ ] Create `UserRepository` implementation with EF Core
- [ ] Add EF Core migrations

#### 2.3 User API Controller
- [ ] Create `UserController.cs`
- [ ] Implement `POST /api/v1/user` endpoint
  - Input: { userName, password }
  - Output: { userId, userName }
- [ ] Add input validation
- [ ] Add error handling

**Expected Outcome**: Users can be created via API. User data persists to SQL.

---

### Phase 3: Product Domain
**Goal**: Implement product catalog

#### 3.1 Product Domain Model
- [ ] Create `Product` aggregate root
  - Properties: ProductId, Name, Price, Stock, CreatedAt
  - Methods: Create(), UpdatePrice(), DecreaseStock()
- [ ] Create `ProductDomainEvents.cs`
  - `ProductCreatedEvent`
- [ ] Create `IProductRepository` interface
- [ ] Create `ProductService` domain service

#### 3.2 Product Persistence
- [ ] Create `ProductRepository` implementation
- [ ] Add EF Core mappings
- [ ] Add migrations

#### 3.3 Product API Controller
- [ ] Create `ProductController.cs`
- [ ] Implement `POST /api/v1/product` endpoint
  - Input: { name, price?, stock? }
  - Output: { productId, name, price, stock }
- [ ] Add validation

**Expected Outcome**: Products can be created. Product catalog available.

---

### Phase 4: Order Domain (Core Business Logic)
**Goal**: Implement order lifecycle with state management

#### 4.1 Order Domain Model
- [ ] Create `Order` aggregate root with state machine
  - States: Pending → PaymentProcessing → Confirmed → Shipped → Delivered
  - Properties: OrderId, UserId, Items, TotalAmount, Currency, Status, CreatedAt
  - Methods:
    - Create(userId, items, currency)
    - StartPaymentProcessing()
    - ConfirmPayment()
    - CancelOrder()
    - StartShipment()
    - MarkAsDelivered()
- [ ] Create `OrderItem` value object
  - Properties: ProductId, Quantity, Price
- [ ] Create `OrderDomainEvents.cs`
  - `OrderCreatedEvent`
  - `PaymentProcessingStartedEvent`
  - `OrderConfirmedEvent`
  - `ShipmentStartedEvent`
  - `OrderDeliveredEvent`
  - `OrderCancelledEvent`
- [ ] Create `IOrderRepository` interface
- [ ] Create `OrderService` domain service

#### 4.2 Order Persistence
- [ ] Create `OrderRepository` implementation
- [ ] Handle `OrderItem` as owned entity in EF Core
- [ ] Add migrations
- [ ] Add indexes on UserId, Status for query performance

#### 4.3 Order API Controller
- [ ] Create `OrderController.cs`
- [ ] Implement `POST /api/v1/user/{user-id}/order` endpoint
  - Input: { items: [{productId, quantity, price}], currency }
  - Validation:
    - User exists
    - Products exist
    - Quantities match inventory
  - Output: { orderId, userId, items, totalAmount, status, createdAt }
- [ ] Implement `GET /api/v1/user/{user-id}/orders` endpoint
- [ ] Implement `GET /api/v1/order/{order-id}` endpoint
- [ ] Publish `OrderCreatedEvent` to RabbitMQ

**Expected Outcome**: Users can place orders. Orders stored with state. Events published.

---

### Phase 5: Message Queue & Event System
**Goal**: Connect domains via event-driven messaging

#### 5.1 RabbitMQ Infrastructure
- [ ] Create `RabbitMQConnection` wrapper
- [ ] Create `RabbitMQPublisher` service
  - Method: PublishEvent(DomainEvent)
- [ ] Create `RabbitMQSubscriber` service
  - Subscribe to queues, handle messages

#### 5.2 Event Publishing
- [ ] Update domain services to publish events after operations
- [ ] Create event exchange and queues in RabbitMQ
  - Exchange: `shopping-events`
  - Queues: `order-events`, `payment-events`, `shipping-events`

#### 5.3 Event Handlers
- [ ] Create `EventSubscriber` base class
- [ ] Implement event handlers that react to domain events
  - Example: `OrderCreatedEventHandler` (triggers payment processing)

**Expected Outcome**: Domains communicate asynchronously via events.

---

### Phase 6: Payment Domain
**Goal**: Implement payment processing

#### 6.1 Payment Domain Model
- [ ] Create `Payment` aggregate root
  - Properties: PaymentId, OrderId, Amount, Currency, Status, CreatedAt
  - States: Pending → Processing → Completed/Failed
  - Methods: Create(), ProcessPayment(), MarkAsCompleted(), MarkAsFailed()
- [ ] Create `PaymentDomainEvents.cs`
  - `PaymentCreatedEvent`
  - `PaymentProcessedEvent`
  - `PaymentFailedEvent`
- [ ] Create `IPaymentRepository` interface
- [ ] Create `PaymentService` domain service

#### 6.2 Payment Persistence
- [ ] Create `PaymentRepository` implementation
- [ ] Add migrations

#### 6.3 Payment Event Handlers
- [ ] Subscribe to `OrderCreatedEvent`
- [ ] Create payment record
- [ ] Simulate payment processing (or integrate with payment gateway)
- [ ] Publish `PaymentProcessedEvent` → triggers order confirmation
- [ ] Handle payment failures → publish `PaymentFailedEvent`

#### 6.4 Payment API (Optional)
- [ ] `GET /api/v1/payment/{payment-id}` - retrieve payment details

**Expected Outcome**: Payment processing triggered by orders. Events flow: Order → Payment → Confirmation.

---

### Phase 7: Shipping Domain
**Goal**: Implement shipment tracking

#### 7.1 Shipping Domain Model
- [ ] Create `Shipment` aggregate root
  - Properties: ShipmentId, OrderId, Status, TrackingNumber, CreatedAt, EstimatedDelivery
  - States: Created → InTransit → Delivered
  - Methods: Create(), MarkAsInTransit(), MarkAsDelivered()
- [ ] Create `ShippingDomainEvents.cs`
  - `ShipmentCreatedEvent`
  - `ShipmentInTransitEvent`
  - `ShipmentDeliveredEvent`
- [ ] Create `IShipmentRepository` interface
- [ ] Create `ShippingService` domain service

#### 7.2 Shipping Persistence
- [ ] Create `ShipmentRepository` implementation
- [ ] Add migrations

#### 7.3 Shipping Event Handlers
- [ ] Subscribe to `OrderConfirmedEvent`
- [ ] Create shipment record with tracking
- [ ] Simulate shipment process (mark as in-transit, delivered after delay)
- [ ] Publish `ShipmentDeliveredEvent`

#### 7.4 Shipping API (Optional)
- [ ] `GET /api/v1/shipment/{shipment-id}` - track shipment

**Expected Outcome**: Shipments created on order confirmation. Full lifecycle: Order → Payment → Confirmation → Shipment → Delivery.

---

### Phase 8: Testing & Integration
**Goal**: Ensure system works end-to-end

#### 8.1 Unit Tests
- [ ] Test domain models (aggregates, value objects)
- [ ] Test services and repositories
- [ ] Test event publishing

#### 8.2 Integration Tests
- [ ] Test API endpoints
- [ ] Test message queue flow
- [ ] Test database persistence

#### 8.3 End-to-End Flow Testing
- [ ] Test complete flow: Create user → Create product → Place order → Payment → Confirm → Ship → Deliver

**Expected Outcome**: Comprehensive test coverage. Full flow validated.

---

### Phase 9: Documentation & Deployment
**Goal**: Prepare for production readiness

#### 9.1 Documentation
- [ ] API documentation (Swagger comments)
- [ ] DDD architecture guide
- [ ] Database schema documentation
- [ ] Event flow diagrams

#### 9.2 Deployment
- [ ] Docker build optimization
- [ ] Production configuration
- [ ] Environment-specific settings

**Expected Outcome**: Documented, deployable system.

---

## Key DDD Concepts to Apply

### 1. **Aggregates**
- Each domain (User, Product, Order, Payment, Shipping) has an aggregate root
- Aggregate roots enforce invariants and consistency
- Repositories work with aggregate roots only

### 2. **Domain Events**
- Each state change in aggregates raises domain events
- Events are immutable records of what happened
- Events flow through RabbitMQ to decouple domains

### 3. **Bounded Contexts**
- Each domain is separate: User, Product, Order, Payment, Shipping
- Explicit boundaries prevent domain model leakage
- DTOs convert between contexts

### 4. **Repository Pattern**
- One repository per aggregate root
- Repositories abstract persistence details
- Repositories enforce domain invariants

### 5. **Value Objects**
- OrderItem, Money, Currency (if modeled as VO)
- Immutable, with equality based on values
- No database identity

### 6. **Domain Services**
- Business logic that spans aggregates
- Orchestrates across multiple repositories
- Publishes domain events

### 7. **Application Services / Commands**
- Use MediatR for command/query dispatch
- Commands represent use cases
- Commands trigger domain operations and publish events

---

## Database Schema Outline

```sql
-- Users Table
CREATE TABLE Users (
    UserId GUID PRIMARY KEY,
    UserName NVARCHAR(100) NOT NULL UNIQUE,
    PasswordHash NVARCHAR(MAX) NOT NULL,
    CreatedAt DATETIME2 NOT NULL
);

-- Products Table
CREATE TABLE Products (
    ProductId GUID PRIMARY KEY,
    Name NVARCHAR(200) NOT NULL,
    Price DECIMAL(18, 2) NOT NULL,
    Stock INT NOT NULL,
    CreatedAt DATETIME2 NOT NULL
);

-- Orders Table
CREATE TABLE Orders (
    OrderId GUID PRIMARY KEY,
    UserId GUID NOT NULL FOREIGN KEY REFERENCES Users(UserId),
    Status NVARCHAR(50) NOT NULL, -- Pending, PaymentProcessing, Confirmed, Shipped, Delivered
    TotalAmount DECIMAL(18, 2) NOT NULL,
    Currency NVARCHAR(3) NOT NULL,
    CreatedAt DATETIME2 NOT NULL,
    INDEX idx_UserId (UserId),
    INDEX idx_Status (Status)
);

-- OrderItems Table (owned by Order)
CREATE TABLE OrderItems (
    OrderItemId GUID PRIMARY KEY,
    OrderId GUID NOT NULL FOREIGN KEY REFERENCES Orders(OrderId),
    ProductId GUID NOT NULL FOREIGN KEY REFERENCES Products(ProductId),
    Quantity INT NOT NULL,
    Price DECIMAL(18, 2) NOT NULL
);

-- Payments Table
CREATE TABLE Payments (
    PaymentId GUID PRIMARY KEY,
    OrderId GUID NOT NULL FOREIGN KEY REFERENCES Orders(OrderId),
    Amount DECIMAL(18, 2) NOT NULL,
    Currency NVARCHAR(3) NOT NULL,
    Status NVARCHAR(50) NOT NULL, -- Pending, Processing, Completed, Failed
    CreatedAt DATETIME2 NOT NULL,
    INDEX idx_OrderId (OrderId)
);

-- Shipments Table
CREATE TABLE Shipments (
    ShipmentId GUID PRIMARY KEY,
    OrderId GUID NOT NULL FOREIGN KEY REFERENCES Orders(OrderId),
    TrackingNumber NVARCHAR(100) NOT NULL,
    Status NVARCHAR(50) NOT NULL, -- Created, InTransit, Delivered
    EstimatedDelivery DATE,
    CreatedAt DATETIME2 NOT NULL,
    INDEX idx_OrderId (OrderId)
);
```

---

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Eventual consistency delays | Clear status states, polling mechanisms, user notifications |
| Message loss in RabbitMQ | Persistent queues, message acknowledgment, retry logic |
| Database schema evolution | EF Core migrations, version control of schema |
| Cross-domain data consistency | Bounded contexts + eventual consistency model |
| Complex state transitions | Explicit state machine in Order aggregate, unit tests |

---

## Success Criteria

- [ ] All 3 core APIs working (User, Product, Order)
- [ ] Order lifecycle complete: create → pay → confirm → ship → deliver
- [ ] Events flowing through RabbitMQ without errors
- [ ] All CRUD operations tested
- [ ] No blocking calls; async messaging where appropriate
- [ ] Clean separation of concerns across domains
