# **Clean Architecture in .NET – A Scalable and Maintainable Approach 🚀**

> **TL;DR:** Clean Architecture separates code into 4 layers: **Domain** (entities), **Application** (business logic), **Infrastructure** (databases/APIs), and **API** (endpoints). This ensures testability, maintainability, and independence from frameworks. Organize by feature, not by file type.

⏱️ *8 min read*

---

## **Introduction**
Clean Architecture is a structured approach to software design that promotes **scalability, maintainability, and separation of concerns**. By organizing code into independent layers, it ensures **modularity, testability, and flexibility**, making it ideal for **microservices and enterprise applications**.

In this guide, we’ll break down a **Clean Architecture implementation in .NET**, following best practices to achieve **clear separation of business logic, infrastructure, and presentation layers**.

---

## **🛠 Project Structure in Clean Architecture**
This architecture consists of **four main layers**, each with a specific role:

### **1️⃣ Domain Layer (Core Business Entities & Rules)**
📌 **Purpose:** Defines the **core business logic** without dependencies on external frameworks.

✅ **Key Features:**  
- Contains **Entities (Domain Models)** representing the core business rules.  
- Includes **Enums** for domain-specific classifications.  
- **No references** to any other layers—this ensures complete **independence and testability**.

📂 **Example Folder Structure:**
/Domain
├── Entities/
│ ├── Order.cs
│ ├── Product.cs
├── Enums/
│ ├── OrderStatus.cs

---

### **2️⃣ Application Layer (Business Logic & Use Cases)**
📌 **Purpose:** Contains **all business rules** and defines **interfaces** for external dependencies.

✅ **Key Features:**  
- References **Domain Layer**.  
- Organized **vertically by feature**, **not horizontally by file type** (e.g., no separate folders for services, repositories, etc.).  
- Defines **interfaces for infrastructure components** (e.g., databases, external APIs, messaging systems).

📂 **Example Folder Structure:**
/Application
├── Orders/
│ ├── Commands/
│ │ ├── CreateOrderCommand.cs
│ ├── Queries/
│ │ ├── GetOrderByIdQuery.cs
├── Interfaces/
│ ├── IOrderRepository.cs
│ ├── IMessageBus.cs

---

### **3️⃣ Infrastructure Layer (External Systems & Implementations)**
📌 **Purpose:** Implements **all external interactions** such as databases, messaging queues, and third-party services.

✅ **Key Features:**  
- **References the Application Layer** (but not the Domain Layer directly).  
- Implements **database access, external APIs, and messaging infrastructure**.  
- Contains a **Persisted folder** where the **DbContext and Migrations** are stored.  
- Does **not contain business logic**—only technical implementation details.

📂 **Example Folder Structure:**
/Infrastructure
├── Persistence/
│ ├── ApplicationDbContext.cs
│ ├── Migrations/
├── Repositories/
│ ├── OrderRepository.cs
├── Services/
│ ├── MessageBusService.cs

---

### **4️⃣ API Layer (Presentation & Endpoints)**
📌 **Purpose:** Exposes the application’s **public-facing APIs** to clients (e.g., Web, Mobile, or other services).

✅ **Key Features:**  
- References the **Application Layer** to invoke use cases.  
- Implements **Controllers and Endpoints** following **REST** or **gRPC**.  
- No business logic—only responsible for **request validation and response formatting**.

📂 **Example Folder Structure:**
/API
├── Controllers/
│ ├── OrdersController.cs
├── Middlewares/
├── Program.cs

---

## **🛠 Handling Database Migrations with a Separate DB Migrator**
📌 **Why a Dedicated Migrator?**  
Instead of letting the **main service apply migrations**, a **dedicated `DbMigrator` project** manages database schema updates.

✅ **Key Benefits:**  
- Prevents **migration conflicts** when running multiple service replicas.  
- Ensures **controlled database updates** without downtime.  
- Supports **seeding for both production and testing** environments.

📂 **Example Folder Structure:**
/DbMigrator
├── Program.cs
├── MigrationService.cs
├── SeedData.cs

💡 **How It Works:**  
- Reads **migrations from the Infrastructure layer**.  
- Applies **migrations and seed data** before the main service starts.

---

## **📦 Stock & Booking Management in Clean Architecture**
### **🔹 Stock Movements vs. Real-Time Calculation**
Stock and availability data should always reflect **real-time** values, but instead of recalculating stock every time, we maintain:
- A **Stock table** that stores the real-time quantity.
- A **Stock Movements table** that tracks every action (e.g., Restock, Damage, Booking, Return).
- A **Booking table** to handle reservations and ensure non-overlapping schedules.

**Formula for Available Stock:**
Available = Stock.Quantity - SUM(OrderService.OrderedQuantity WHERE Paid = FALSE) - SUM(ShoppingCart.Quantity)


### **🔹 Example Movement Types**
| MovementTypeId | Name       | Notes |
|---------------|-----------|-------|
| 1             | Restock   | New items added to stock |
| 2             | Damage    | Items marked as damaged |
| 3             | Booking   | Reserved for customers |
| 4             | Return    | Items returned to stock |
| 5             | Shipped   | Items shipped to customers |

---

## **🚀 Why Use This Clean Architecture Approach?**
✔ **Scalability** – Services and modules can be extended independently.  
✔ **Maintainability** – Clear separation of concerns makes the system easy to modify.  
✔ **Testability** – Business logic can be tested in isolation.  
✔ **Extensibility** – New features can be introduced without breaking existing functionality.  

---

## **📌 Conclusion**
This **Clean Architecture** approach ensures a well-structured, scalable, and maintainable codebase in .NET. By separating **Domain, Application, Infrastructure, and API layers**, we keep business logic isolated, optimize database interactions, and streamline deployment processes.

---

## 🚀 Stay Connected
🔗 **Learn More:** [Cycolis Software](https://cycolis-software.com)  
💻 **Explore Our Work:** [GitHub](https://github.com/Cycolis-Software)  
💼 **Connect on LinkedIn:** [LinkedIn](https://www.linkedin.com/company/cycolis-software)  
🐦 **Follow for Updates:** [X (Twitter)](https://x.com/CycolisSoftware) 
