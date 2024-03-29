# Use MySQL HeatWave For Development with JavaScript

## Introduction

MySQL HeatWave can easily be used for development tasks with existing Oracle services, such as Oracle Cloud Analytics. New applications can also be created with the LAMP or other software stacks.

_Estimated Time:_ 15 minutes

### Objectives

In this lab, you will be guided through the following task:

- In this lab, you will be guided through the following tasks:

- Install Apache and PHP
- Create PHP / MYSQL Connect Application

### Prerequisites

- An Oracle Trial or Paid Cloud Account


## Task 1: Install Apache

1. If not already connected with SSH, on Command Line, connect to the Compute instance using SSH ... be sure replace the  "private key file"  and the "new compute instance ip"

     ```bash
    <copy>ssh -i private_key_file opc@new_compute_instance_ip</copy>
     ```

2.	Install app server

    a. Install Apache
    
    ```bash
    <copy>sudo yum install httpd -y </copy>
    ```
    b. Enable Apache

    ```bash
    <copy>sudo systemctl enable httpd</copy>
    ```
    c. Start Apache

    ```bash
    <copy>sudo systemctl restart httpd</copy>
    ```
    d. Setup firewall

    ```bash
    <copy>sudo firewall-cmd --permanent --add-port=80/tcp</copy>
    ```
    
    e. Reload firewall

    ```bash
    <copy>sudo firewall-cmd --reload</copy>
    ```

3.	From a browser test apache from your loacal machine using the Public IP Address of your Compute Instance

    **Example: http://129.213....**

## Task 2: Install PHP

1.	Install php:

    a. Install php:7.4

    ```bash
    <copy> sudo dnf module install php:7.4 -y</copy>
    ```
     
    b. Install associated php libraries

    ```bash
    <copy>sudo yum install php-cli php-mysqlnd php-zip php-gd php-mbstring php-xml php-json -y</copy>
    ```

    c. View  php / mysql libraries

    ```bash
    <copy>php -m |grep mysql</copy>
    ```
    d. View php version

    ```bash
    <copy>php -v</copy>
    ```
    e. Restart Apache

    ```bash
    <copy>sudo systemctl restart httpd</copy>
    ```

2.	Create test php file (info.php)

    ```bash
    <copy>sudo nano /var/www/html/info.php</copy>
    ```
3. Add the following code to the editor and save the file (ctr + o) (ctl + x)

    ```bash
    <copy><?php
phpinfo();
?></copy>
    ```
4. From your local machine, browse the page info.php

   Example: http://129.213.167.../info.php

## Task 3: Verify connection to the Database

1. Perform a security update: set SELinux to allow Apache to connect to MySQL

    ```bash
    <copy> sudo setsebool -P httpd_can_network_connect 1 </copy>
    ```

2.	Create config.php

    ```bash
    <copy>cd /var/www/html</copy>
    ```

    ```bash
    <copy>sudo nano config.php</copy>
    ```
3. Add the following code to the editor and save the file (ctr + o) (ctl + x). Make sure to add your database IP, user and password

    ```bash
    <copy>
    <?php
    // Database credentials
    define('DB_SERVER', '10.0.1...');// HeatWave DB server IP address
    define('DB_USERNAME', 'admin');
    define('DB_PASSWORD', 'Welcome#12345');
    define('DB_NAME', 'sakila');
    //Attempt to connect to MySQL database
    $link = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD, DB_NAME);
    // Check connection
    if($link === false){
        die("ERROR: Could not connect. " . mysqli_connect_error());
    }
    // Print host information
    echo 'Successfull Connect.';
    echo 'Host info: ' . mysqli_get_host_info($link);
    ?> 
</copy>
    ```

    - Test Config.php on Web sever http://150.230..../config.php

## Task 4: Create application to display JavaScript function's result


1.	Create dbtest.php

    ```bash
    <copy>cd /var/www/html</copy>
    ```

    ```bash
    <copy>sudo nano dbtest.php</copy>
    ```

2. Add the following code to the editor and save the file (ctr + o) (ctl + x)

    ```bash
    <copy>
    <?php
    require_once 'config.php';

    try {
        // connect to MySQL server
        $conn = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    
        // call the add_nos
        $sql = 'SELECT add_nos(12,52);';
        $stmt = $conn->prepare($sql);

        // bind the task id
        $task_id = 1;
        $stmt->bindParam(':value', $value, PDO::PARAM_INT);

        // execute the query & close the cursor
        $stmt->execute();
        $stmt->closeCursor();

        // execute the second query to get the task status
        echo "HeatWave JavaScript Function call test: ";
        echo "<br>";
        echo "SELECT add_nos(12,52);";
        echo "<br>";
        $row = $conn->query('SELECT add_nos(12,52) as value;')->fetch(PDO::FETCH_ASSOC);
        if ($row) {
            echo $row !== false ? $row['value'] : null;
        }
    } catch (PDOException $e) {
        die($e);
    }

</copy>
    ```

3. From your local  machine connect to dbtest.php

    Example: http://129.213.167..../dbtest.php  


## Acknowledgements
- **Author** - Perside Foster, MySQL Solution Engineering
- **Contributors** - Nick Mader, MySQL Global Channel Enablement & Strategy Manager
- **Last Updated By/Date** - Perside Foster, MySQL Solution Engineering, October 2023