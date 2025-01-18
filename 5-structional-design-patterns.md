# **Structural Design Patterns in React: Practical Use Cases & Code Examples**

## **Introduction**
Structural design patterns focus on organizing relationships between components, making systems more flexible and scalable. In React, these patterns help manage UI complexity, ensure better component composition, and improve maintainability.

In this article, we’ll explore **common Structural Patterns**, their practical **React use cases**, and how they compare to their general software engineering applications.

---

## **1. Adapter Pattern — Bridging Incompatible Interfaces**
### **What It Does:**
The **Adapter Pattern** allows incompatible interfaces to work together by providing a wrapper around an existing class or function.

### **Example: Converting API Response Data to Match UI Needs**
```tsx
const apiResponse = {
  fullName: "John Doe",
  userAge: 30,
};

const adaptUserData = (data) => ({
  name: data.fullName,
  age: data.userAge,
});

const UserProfile = ({ user }) => (
  <div>
    <h2>{user.name}</h2>
    <p>Age: {user.age}</p>
  </div>
);

const App = () => {
  const adaptedUser = adaptUserData(apiResponse);
  return <UserProfile user={adaptedUser} />;
};
```

### **💡 When to Use It?**
- When API responses **don’t match the frontend model**.
- When integrating **third-party libraries** that don’t follow your structure.

---

## **2. Decorator Pattern — Enhancing Components Dynamically**
### **What It Does:**
The **Decorator Pattern** allows behavior to be added dynamically to an object without modifying its structure.

### **Example: Higher-Order Component (HOC) for Authorization**
```tsx
const withAuth = (WrappedComponent) => {
  return (props) => {
    const isAuthenticated = true; // Assume user is authenticated
    return isAuthenticated ? <WrappedComponent {...props} /> : <p>Access Denied</p>;
  };
};

const Dashboard = () => <h2>Welcome to Dashboard</h2>;
const ProtectedDashboard = withAuth(Dashboard);

const App = () => <ProtectedDashboard />;
```

### **💡 When to Use It?**
- When adding **authentication logic** to multiple components.
- When applying **logging, analytics, or styling enhancements** to existing components.

---

## **3. Facade Pattern — Simplifying Complex Operations**
### **What It Does:**
The **Facade Pattern** provides a simple interface to a complex subsystem, reducing dependencies between components.

### **Example: Abstracting API Calls into a Service Module**
```tsx
const UserService = {
  async fetchUser() {
    const response = await fetch("https://api.example.com/user");
    return response.json();
  },
};

const UserProfile = async () => {
  const user = await UserService.fetchUser();
  return (
    <div>
      <h2>{user.name}</h2>
      <p>Age: {user.age}</p>
    </div>
  );
};
```

### **💡 When to Use It?**
- When you want to **hide complexity** in service modules.
- When handling **API calls, caching, or data formatting**.

---

## **4. Proxy Pattern — Controlling Component Access**
### **What It Does:**
The **Proxy Pattern** acts as a substitute to control access to an object, often used for caching, logging, or permission checks.

### **Example: API Call Caching with Proxy**
```tsx
const apiCache = new Map();

const fetchData = async (url) => {
  if (apiCache.has(url)) {
    return apiCache.get(url);
  }
  const response = await fetch(url);
  const data = await response.json();
  apiCache.set(url, data);
  return data;
};

const App = async () => {
  const data = await fetchData("https://api.example.com/data");
  return <div>Data: {JSON.stringify(data)}</div>;
};
```

### **💡 When to Use It?**
- When implementing **caching mechanisms**.
- When **validating or logging requests** before processing.

---

## **5. Composite Pattern — Nesting Components in a Tree Structure**
### **What It Does:**
The **Composite Pattern** treats individual and composite objects uniformly, making it useful for UI structures like trees or menus.

### **Example: Recursive Component Rendering for a Menu**
```tsx
const MenuItem = ({ item }) => (
  <li>
    {item.name}
    {item.children && (
      <ul>
        {item.children.map((child) => (
          <MenuItem key={child.name} item={child} />
        ))}
      </ul>
    )}
  </li>
);

const menuData = {
  name: "Home",
  children: [
    { name: "Products", children: [{ name: "Laptops" }, { name: "Phones" }] },
    { name: "About" },
  ],
};

const App = () => <ul><MenuItem item={menuData} /></ul>;
```

### **💡 When to Use It?**
- When rendering **nested structures like menus, trees, or file systems**.
- When working with **recursive UI elements**.

---

## **Conclusion**
Structural patterns in React help **simplify component interactions, improve reusability, and make applications more maintainable**.

### **📝 Quick Recap:**
| **Pattern**      | **Use Case in React** |
|-----------------|----------------------|
| **Adapter**     | Transform API data for UI compatibility |
| **Decorator**   | HOCs for authentication, analytics, or styling |
| **Facade**      | Simplifying API calls and data processing |
| **Proxy**       | Caching and request validation |
| **Composite**   | Recursive UI structures (menus, trees) |

---

## **🚀 Next Steps**
- 💬 Have you used any of these patterns in your React projects?
- 🔍 Which one do you find the most useful?

Let me know if you'd like more **real-world examples or deeper explanations**! 🚀🔥

