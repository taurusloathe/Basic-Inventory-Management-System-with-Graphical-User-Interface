import tkinter as tk
from tkinter import ttk
import json
from datetime import datetime

class UniqueInventory:
    def __init__(self):
        self.products = {}
        self.transactions = []
        self.load_inventory()  # Load inventory from file when creating an instance

    def add_product(self, name, price, quantity):
        if name in self.products:
            self.products[name]['quantity'] += quantity
        else:
            self.products[name] = {'price': price, 'quantity': quantity, 'location': 'Shelf A'}
        self.save_inventory()  # Save inventory after adding a product

    def receive_purchase_order(self, order_details):
        for item in order_details:
            name = item['name']
            quantity = item['quantity']
            self.add_product(name, 0, quantity)  # Assuming price is 0 for simplicity
            self.transactions.append({'type': 'Receive', 'name': name, 'quantity': quantity, 'timestamp': datetime.now().strftime("%Y-%m-%d %H:%M:%S")})
        self.save_inventory()  # Save inventory after receiving a purchase order

    def display_transactions(self):
        transactions_str = "Transaction History:\n"
        for transaction in self.transactions:
            transactions_str += f"Type: {transaction['type']}, Product: {transaction['name']}, Quantity: {transaction['quantity']}, Time: {transaction['timestamp']}\n"
        return transactions_str

    def display_inventory(self):
        inventory_str = "Current Inventory:\n"
        for product, details in self.products.items():
            inventory_str += f"Product: {product}, Price: ${details['price']}, Quantity: {details['quantity']}, Location: {details['location']}\n"
        return inventory_str

    def calculate_total_value(self):
        total_value = 0
        for product, details in self.products.items():
            total_value += details['price'] * details['quantity']
        return total_value

    def update_location(self, name, new_location):
        if name in self.products:
            self.products[name]['location'] = new_location
        else:
            return f"Product '{name}' not found in inventory."

    def remove_product(self, name, quantity):
        if name in self.products:
            if quantity <= self.products[name]['quantity']:
                self.products[name]['quantity'] -= quantity
                if self.products[name]['quantity'] == 0:
                    del self.products[name]  # Remove the product if quantity becomes zero
                self.save_inventory()  # Save inventory after removing a product
            else:
                return f"Error: Not enough quantity of '{name}' in inventory."
        else:
            return f"Product '{name}' not found in inventory."

    def search_product(self, name):
        if name in self.products:
            return f"Product found: {name}, Price: ${self.products[name]['price']}, Quantity: {self.products[name]['quantity']}, Location: {self.products[name]['location']}"
        else:
            return f"Product not found: {name}"

    def sort_inventory(self, criterion='name'):
        sorted_inventory = sorted(self.products.items(), key=lambda x: x[1][criterion])
        return dict(sorted_inventory)

    def save_inventory(self, filename='inventory_data.json'):
        with open(filename, 'w') as file:
            json.dump(self.products, file)

    def load_inventory(self, filename='inventory_data.json'):
        try:
            with open(filename, 'r') as file:
                self.products = json.load(file)
        except FileNotFoundError:
            pass

# Function definitions
def add_product():
    name = name_entry.get()
    price_str = price_entry.get()
    quantity_str = quantity_entry.get()

    # Validate and convert price to float
    try:
        price = float(price_str)
    except ValueError:
        display_text.delete(1.0, tk.END)
        display_text.insert(tk.END, "Error: Please enter a valid price (numeric value).\n")
        return

    # Validate and convert quantity to int
    try:
        quantity = int(quantity_str)
    except ValueError:
        display_text.delete(1.0, tk.END)
        display_text.insert(tk.END, "Error: Please enter a valid quantity (integer value).\n")
        return

    inventory.add_product(name, price, quantity)
    display_inventory()

def remove_product():
    name = name_entry.get()
    quantity_str = quantity_entry.get()

    # Validate and convert quantity to int
    try:
        quantity = int(quantity_str)
    except ValueError:
        display_text.delete(1.0, tk.END)
        display_text.insert(tk.END, "Error: Please enter a valid quantity (integer value).\n")
        return

    result = inventory.remove_product(name, quantity)
    display_inventory()
    display_text.insert(tk.END, result + "\n")

def update_location():
    name = name_entry.get()
    new_location = location_entry.get()
    result = inventory.update_location(name, new_location)
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, result)

def display_inventory():
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, inventory.display_inventory())

def calculate_total_value():
    total_value = inventory.calculate_total_value()
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, f'Total Inventory Value: ${total_value}')

def search():
    name = name_entry.get()
    result = inventory.search_product(name)
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, result)

def sort_inventory():
    criterion = sort_criterion.get()
    sorted_products = inventory.sort_inventory(criterion)
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, "Sorted Inventory:\n")
    for product, details in sorted_products.items():
        display_text.insert(tk.END, f"Product: {product}, Price: ${details['price']}, Quantity: {details['quantity']}, Location: {details['location']}\n")

# Function definitions for the new features
def receive_purchase_order():
    order_details_str = order_details_entry.get("1.0",'end-1c')
    try:
        order_details = json.loads(order_details_str)
    except json.JSONDecodeError:
        display_text.delete(1.0, tk.END)
        display_text.insert(tk.END, "Error: Invalid JSON format for order details.\n")
        return

    inventory.receive_purchase_order(order_details)
    display_transactions()

def display_transactions():
    display_text.delete(1.0, tk.END)
    display_text.insert(tk.END, inventory.display_transactions())

# Create a GUI window
root = tk.Tk()
root.title("Inventory Management")

# Configure the style
style = ttk.Style()
style.configure("TLabel", foreground="#ff8c00", background="#483d8b")  # DarkOrange text on DarkSlateBlue background
style.configure("TEntry", foreground="#000000", background="#d3d3d3")  # Black text on LightGrey background
style.configure("TButton", foreground="#000000", background="#2e8b57")  # Black text on SeaGreen background
style.configure("TText", foreground="#000000", background="#d3d3d3")  # Black text on LightGrey background

# Create an instance of the inventory
inventory = UniqueInventory()

# Create labels, entry fields, and buttons with the updated style
name_label = ttk.Label(root, text="Product Name:")
name_label.pack()

name_entry = ttk.Entry(root)
name_entry.pack()

price_label = ttk.Label(root, text="Price:")
price_label.pack()

price_entry = ttk.Entry(root)
price_entry.pack()

quantity_label = ttk.Label(root, text="Quantity:")
quantity_label.pack()

quantity_entry = ttk.Entry(root)
quantity_entry.pack()

location_label = ttk.Label(root, text="New Location:")
location_label.pack()

location_entry = ttk.Entry(root)
location_entry.pack()

add_button = ttk.Button(root, text="Add Product", command=add_product)
add_button.pack()

remove_button = ttk.Button(root, text="Remove Product", command=remove_product)
remove_button.pack()

update_button = ttk.Button(root, text="Update Location", command=update_location)
update_button.pack()

display_button = ttk.Button(root, text="Display Inventory", command=display_inventory)
display_button.pack()

calculate_button = ttk.Button(root, text="Calculate Total Value", command=calculate_total_value)
calculate_button.pack()

search_button = ttk.Button(root, text="Search Product", command=search)
search_button.pack()

# Entry field for sorting criterion
sort_criterion_label = ttk.Label(root, text="Sort Criterion:")
sort_criterion_label.pack()

sort_criterion = ttk.Combobox(root, values=['name', 'price', 'quantity', 'location'])
sort_criterion.set('name')
sort_criterion.pack()

sort_button = ttk.Button(root, text="Sort Inventory", command=sort_inventory)
sort_button.pack()

# Entry field for order details (JSON format)
order_details_label = ttk.Label(root, text="Order Details (JSON format):")
order_details_label.pack()

order_details_entry = tk.Text(root, height=5, width=40)
order_details_entry.pack()

receive_order_button = ttk.Button(root, text="Receive Purchase Order", command=receive_purchase_order)
receive_order_button.pack()

transactions_button = ttk.Button(root, text="Display Transactions", command=display_transactions)
transactions_button.pack()

# Use a Text widget for displaying inventory with a black foreground on a LightGrey background
display_text = tk.Text(root, height=10, width=40, foreground="#000000", background="#d3d3d3")
display_text.pack()

# Save inventory when the window is closed
root.protocol("WM_DELETE_WINDOW", lambda: (inventory.save_inventory(), root.destroy()))

# Start the GUI main loop
root.mainloop()
