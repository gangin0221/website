# MySQL Installation and Configuration Guide

The following steps will help you install and configure MySQL for the Idle View App backend:

## Step 1: Install MySQL

### On Windows:
1. Download the MySQL installer from the [official MySQL website](https://dev.mysql.com/downloads/installer/).
2. Run the installer and select "Custom Installation."
3. Include the following components:
   - MySQL Server
   - MySQL Workbench (optional, for GUI management)
4. Complete the installation and note the MySQL root password.

### On Linux:
1. Update your package index:
   ```sh
   sudo apt update
   ```
2. Install MySQL Server:
   ```sh
   sudo apt install mysql-server
   ```
3. Secure the MySQL installation:
   ```sh
   sudo mysql_secure_installation
   ```
   Follow the prompts to set the root password and secure the database.

### On macOS:
1. Install MySQL using Homebrew:
   ```sh
   brew install mysql
   ```
2. Start the MySQL service:
   ```sh
   brew services start mysql
   ```
3. Secure the MySQL installation:
   ```sh
   mysql_secure_installation
   ```

---

## Step 2: Configure MySQL Database for Idle View App

1. **Log in to the MySQL shell:**
   ```sh
   mysql -u root -p
   ```
2. **Create a database for the project:**
   ```sql
   CREATE DATABASE idleview;
   ```
3. **Create a dedicated user for the project:**
   ```sql
   CREATE USER 'idleview_user'@'%' IDENTIFIED BY 'strong_password';
   ```
4. **Grant the user necessary permissions:**
   ```sql
   GRANT ALL PRIVILEGES ON idleview.* TO 'idleview_user'@'%';
   FLUSH PRIVILEGES;
   ```
5. **Exit the MySQL shell:**
   ```sh
   exit
   ```

---

## Step 3: Update `.env` File

Ensure the `.env` file in the `backend` directory includes the correct database connection details:
```plaintext
DATABASE_URL="mysql://idleview_user:strong_password@localhost:3306/idleview"
```

---

## Step 4: Test the Connection

1. **Navigate to the backend directory:**
   ```sh
   cd backend
   ```
2. **Run database migrations:**
   ```sh
   npx prisma migrate dev
   ```
   This will create the necessary tables in the `idleview` database.
3. **Verify the connection by starting the backend server:**
   ```sh
   npm run start
   ```
   The server should connect to the database without errors.

---

By following these steps, you can set up and configure MySQL to work seamlessly with the Idle View App backend.

