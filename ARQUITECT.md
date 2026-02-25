# Project Summary: OpenMarket Corp. E-Market Platform

**Team:** Jalasoft Jala-U GA SE G3  
**Review Date:** February 25, 2026

## 1. Architecture Characteristics
<img width="983" height="537" alt="image" src="https://github.com/user-attachments/assets/4be2d9cc-6620-4fa5-9acc-68f0071e2292" />
The system was designed by prioritizing three fundamental characteristics (“Top 3 Driving Characteristics”) to support a high-traffic e-commerce environment:

* **Elasticity:** Essential to handle massive traffic spikes without degrading the user experience (Requirement 3.1).
* **Availability:** Critical to prevent system outages during peak sales seasons (Requirement 3.3).
* **Security:** Focused on protecting financial and personal data under the PCI-DSS standard (Requirement 3.4).

> **Note:** **Elasticity** was prioritized over Scalability due to the need to automatically reduce resources when demand decreases in order to optimize costs.
<img width="857" height="475" alt="image" src="https://github.com/user-attachments/assets/6ffa373d-bd11-4889-a524-190a46a0d516" />

## 2. Selected Architectural Style
After evaluating options such as Monoliths and pure event-based architectures, the following was selected:  
**Microservices complemented with Event-Driven communication.**

### Justification for the choice:
* **Independent horizontal scaling:** Each service grows according to its own demand.
* **Fault isolation:** If one service fails, the rest of the system remains operational.
* **Independent deployment:** Enables continuous CI/CD cycles without taking the store offline.
<img width="1068" height="488" alt="image" src="https://github.com/user-attachments/assets/f981c937-9752-4859-8d4c-5db2277e916b" />

## 3. Visual Representation (C4 Model)
The document breaks down the system structure into three levels:
<img width="858" height="394" alt="image" src="https://github.com/user-attachments/assets/a95aec2c-cbde-4c35-ba25-3597eb0b6aea" />

### Level 1: System Context (C1)
Shows **Customers** and **Administrators** interacting with the OpenMarket system, which depends on external services such as Payment Gateways, Supplier Systems (via SFTP/REST), and Logistics APIs.
<img width="1033" height="545" alt="image" src="https://github.com/user-attachments/assets/dc23aadf-c8e9-4179-a9fe-1c0cecd491be" />

### Level 2: Containers (C2)
A diverse and modern technology stack is detailed:
* **Frontend:** Web and Admin Panel in **React**, Mobile in **Flutter**.
* **Gateway:** **Kong API Gateway** centralizes requests.
* **Services:** Order Service (**Node.js**), Catalog Service (**Python**), and Payment Service (**Java**).
* **Persistence:** Mix of SQL (**PostgreSQL**) and NoSQL (**MongoDB**, **Redis**, **ElasticSearch**).
* **Messaging:** **RabbitMQ** for asynchronous communication between services.
<img width="898" height="583" alt="image" src="https://github.com/user-attachments/assets/d24524f7-5ec7-4b73-bf2c-9b4eff6dfc6b" />

### Level 3: Payment Service Components (C3)
The payment service uses resilience patterns such as **Circuit Breaker** (via Resilience4j) to prevent blocking if external APIs (Stripe/PayPal) fail. It also includes a **Fraud Detection Engine** for metadata analysis.
<img width="892" height="520" alt="image" src="https://github.com/user-attachments/assets/0ed8ec75-7f2c-4b81-b05d-d603613afc6f" />

### 4. Code Repository
<img width="872" height="543" alt="image" src="https://github.com/user-attachments/assets/04b4bbc3-f3b2-4eac-af27-b6a8e0e09aed" />

### 5. Class Diagrams
<img width="731" height="584" alt="image" src="https://github.com/user-attachments/assets/a8aa3b3d-3e6e-4339-b7cf-ca4691cdb49f" />

### 6. Distance from the Main Sequence
<img width="715" height="611" alt="image" src="https://github.com/user-attachments/assets/d906b3aa-525b-454a-b991-ab4f7f71b41b" />

### 7. Self-assessment on Team Participation:
Alejandro: 5/5  
Cristian: 5/5  
Román: 5/5  
Said: 5/5  
Sebastián: 5/5
