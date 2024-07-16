# task-6
import tkinter as tk
from tkinter import ttk, messagebox
import requests

class CurrencyConverter:
    def __init__(self, root):
        self.root = root
        self.root.title("USD Currency Converter")
        self.root.geometry("400x300")
        
        self.api_url = "https://api.exchangerate-api.com/v4/latest/USD"
        
        # Get exchange rates
        self.exchange_rates = self.get_exchange_rates()
        
        # Create the GUI elements
        self.create_widgets()

    def get_exchange_rates(self):
        try:
            response = requests.get(self.api_url)
            data = response.json()
            return data['rates']
        except Exception as e:
            messagebox.showerror("Error", f"Failed to retrieve exchange rates: {e}")
            return None

    def create_widgets(self):
        ttk.Label(self.root, text="Amount in USD:").grid(row=0, column=0, padx=10, pady=10, sticky="e")
        self.amount_var = tk.DoubleVar()
        ttk.Entry(self.root, textvariable=self.amount_var).grid(row=0, column=1, padx=10, pady=10)

        ttk.Label(self.root, text="Convert to:").grid(row=1, column=0, padx=10, pady=10, sticky="e")
        self.currency_var = tk.StringVar()
        self.currency_combobox = ttk.Combobox(self.root, textvariable=self.currency_var, state="readonly")
        self.currency_combobox['values'] = list(self.exchange_rates.keys())
        self.currency_combobox.grid(row=1, column=1, padx=10, pady=10)

        ttk.Button(self.root, text="Convert", command=self.convert_currency).grid(row=2, column=0, columnspan=2, pady=20)

        self.result_var = tk.StringVar()
        ttk.Label(self.root, textvariable=self.result_var, font=("Arial", 14)).grid(row=3, column=0, columnspan=2, pady=10)

    def convert_currency(self):
        amount = self.amount_var.get()
        target_currency = self.currency_var.get()

        if amount <= 0:
            messagebox.showwarning("Invalid Input", "Please enter a positive amount.")
            return

        if target_currency == "":
            messagebox.showwarning("Invalid Input", "Please select a target currency.")
            return

        exchange_rate = self.exchange_rates.get(target_currency)
        converted_amount = amount * exchange_rate
        self.result_var.set(f"{amount} USD = {converted_amount:.2f} {target_currency}")

if __name__ == "__main__":
    root = tk.Tk()
    app = CurrencyConverter(root)
    root.mainloop()
