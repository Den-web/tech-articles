# **Creational Design Patterns in React with Practical Examples**  

## **1. Factory Pattern** – 🔄 *Creating Reusable and Dynamic Components*  
### **What It Does:**  
The **Factory Pattern** provides a way to create objects without specifying their exact class. In React, it's useful for dynamically rendering components based on conditions.

### **Example: Factory Function for Buttons**
We create a factory function to generate different button types dynamically.

```tsx
const ButtonFactory = ({ type, label }: { type: "primary" | "secondary"; label: string }) => {
  if (type === "primary") {
    return <button className="bg-blue-500 text-white px-4 py-2">{label}</button>;
  }
  return <button className="bg-gray-300 text-black px-4 py-2">{label}</button>;
};

const App = () => (
  <div>
    <ButtonFactory type="primary" label="Primary Button" />
    <ButtonFactory type="secondary" label="Secondary Button" />
  </div>
);
```

### **💡 When to Use It?**  
- When you need to **dynamically create UI components** based on a prop or condition.  
- When implementing **theme-based UI components**.  

---

## **2. Singleton Pattern** – 🔁 *Ensuring a Single Instance for Global State*  
### **What It Does:**  
The **Singleton Pattern** ensures **only one instance** of an object exists. This is useful in React for **global state management**.

### **Example: Singleton for Global Theme State**
```tsx
class ThemeState {
  private static instance: ThemeState;
  public theme: "light" | "dark" = "light";

  private constructor() {} // Private constructor prevents direct instantiation

  static getInstance(): ThemeState {
    if (!ThemeState.instance) {
      ThemeState.instance = new ThemeState();
    }
    return ThemeState.instance;
  }

  toggleTheme() {
    this.theme = this.theme === "light" ? "dark" : "light";
  }
}

const themeStore = ThemeState.getInstance();
themeStore.toggleTheme();
console.log(themeStore.theme); // dark
```

### **💡 When to Use It?**  
- For **global state management** without an external library like Redux.  
- When you need to **share application-wide settings**, like themes or language preferences.  

---

## **3. Prototype Pattern** – 📑 *Efficient Cloning of Objects in React State*  
### **What It Does:**  
The **Prototype Pattern** allows objects to be cloned, which is useful in **React state updates without mutation**.

### **Example: Cloning State in React**
```tsx
const [user, setUser] = useState({ name: "Alice", age: 25 });

const incrementAge = () => {
  setUser(prevUser => ({ ...prevUser, age: prevUser.age + 1 })); // Clone object before updating
};
```

### **💡 When to Use It?**  
- When you need to **update React state immutably**.  
- When dealing with **nested state objects**.  

---

## **4. Builder Pattern** – 🏗️ *Configurable Component Creation*  
### **What It Does:**  
The **Builder Pattern** is useful when creating complex components with **multiple optional configurations**.

### **Example: Dynamic Card Builder**
```tsx
class CardBuilder {
  private card: { title?: string; description?: string; image?: string } = {};

  setTitle(title: string) {
    this.card.title = title;
    return this;
  }

  setDescription(description: string) {
    this.card.description = description;
    return this;
  }

  setImage(image: string) {
    this.card.image = image;
    return this;
  }

  build() {
    return this.card;
  }
}

// Usage
const card = new CardBuilder()
  .setTitle("React Builder Pattern")
  .setDescription("Learn to use the Builder Pattern in React")
  .setImage("image.png")
  .build();

console.log(card);
```

### **💡 When to Use It?**  
- When creating **configurable UI components** like forms, modals, and widgets.  
- When you want a **step-by-step component-building process**.  

---

## **5. Abstract Factory Pattern** – 🏭 *Creating Families of Related Components*  
### **What It Does:**  
The **Abstract Factory Pattern** provides an interface for creating related objects **without specifying their concrete classes**.

### **Example: Theme-based Button Factory**
```tsx
const LightButton = () => <button className="bg-white text-black">Light Mode</button>;
const DarkButton = () => <button className="bg-black text-white">Dark Mode</button>;

const ThemeFactory = (theme: "light" | "dark") => {
  return theme === "light" ? <LightButton /> : <DarkButton />;
};

const App = () => {
  const theme = "dark"; // Assume theme is obtained from context
  return <div>{ThemeFactory(theme)}</div>;
};
```

### **💡 When to Use It?**  
- When implementing **theme-based UI components**.  
- When designing **component libraries** that support multiple themes or styles.  

---

## **Conclusion**
Creational patterns help improve **scalability, maintainability, and reusability** in React applications. Understanding their use cases makes your code more **structured and efficient**.

### **📝 Quick Recap:**
| **Pattern**           | **Use Case in React** |
|----------------------|---------------------|
| **Factory**         | Dynamic component rendering |
| **Singleton**       | Global state management (e.g., theme, auth) |
| **Prototype**       | Efficient state updates without mutation |
| **Builder**         | Creating flexible, configurable UI components |
| **Abstract Factory** | Theming and UI component libraries |

---

## **🚀 Next Steps**
- 💬 Have you used any of these patterns in your React projects?  
- 🔍 Which one do you find the most useful?
