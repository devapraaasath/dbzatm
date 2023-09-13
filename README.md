import mysql.connector

# Function to establish a database connection
def connect_to_database():
    try:
        conn = mysql.connector.connect(
            host="localhost",
            user="root",
            password="12345",
            database="dbz_bank"
        )
        return conn
    except mysql.connector.Error as err:
        print("Error: {}".format(err))
        return None

# Function to create a new user account
def create_account(conn, account_number, pin, balance):
    try:
        cursor = conn.cursor()
        # Replace 'user_accounts' with your actual table name
        query = "INSERT INTO user_accounts (account_number, pin, balance) VALUES (%s, %s, %s)"
        data = (account_number, pin, balance)
        cursor.execute(query, data)
        conn.commit()
        cursor.close()
        return True
    except mysql.connector.Error as err:
        print("Error: {}".format(err))
        return False

# Function to check account balance
def check_balance(conn, account_number, pin):
    try:
        cursor = conn.cursor()
        # Replace 'user_accounts' with your actual table name
        query = "SELECT balance FROM user_accounts WHERE account_number = %s AND pin = %s"
        data = (account_number, pin)
        cursor.execute(query, data)
        result = cursor.fetchone()
        cursor.close()
        if result:
            return result[0]  # Return the balance
        else:
            return None  # Account not found
    except mysql.connector.Error as err:
        print("Error: {}".format(err))
        return None

# Example usage:
if __name__ == "__main__":
    conn = connect_to_database()
    if conn:
        account_number = "123456"
        pin = "1234"
        balance = 1000.00

        if create_account(conn, account_number, pin, balance):
            print("Account created successfully.")
        else:
            print("Failed to create an account.")

        entered_pin = input("Enter your PIN: ")
        balance = check_balance(conn, account_number, entered_pin)
        if balance is not None:
            print("Your balance is: ${:.2f}".format(balance))
        else:
            print("Invalid account number or PIN.")

        conn.close()  # Corrected the typo here (changed 'cnn' to 'conn')
