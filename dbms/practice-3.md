# Advanced SQL Queries for E-Commerce Customer Management

## Query 1: Retrieve Full Customer Profile
  SELECT 
      C.CustomerID,
      C.FirstName,
      C.MiddleName,
      C.LastName,
      C.Roles,
      CI.ContactType,
      CI.ContactValue,
      CI.Purpose AS ContactPurpose,
      A.StreetAddress,
      A.City,
      A.StateProvince,
      A.PostalCode,
      A.Country,
      A.Purpose AS AddressPurpose
  FROM Customer C
  LEFT JOIN ContactInfo CI ON C.CustomerID = CI.CustomerID
  LEFT JOIN Address A ON C.CustomerID = A.CustomerID
  WHERE CI.ContactType IS NOT NULL OR A.StreetAddress IS NOT NULL;


## Query 2: Find Customers with Multiple Roles
  SELECT CustomerID, FirstName, LastName, Roles
  FROM Customer
  WHERE Roles LIKE '%,' OR Roles LIKE '%,%';

## Query 3: List Customers and Their Most Recent Email Address
  SELECT 
      C.CustomerID,
      C.FirstName,
      C.LastName,
      CI.ContactValue AS MostRecentEmail
  FROM Customer C
  JOIN ContactInfo CI ON C.CustomerID = CI.CustomerID
  WHERE CI.ContactType = 'Email'
  AND CI.ContactValue = (
      SELECT MAX(ContactValue) 
      FROM ContactInfo 
      WHERE CustomerID = C.CustomerID 
      AND ContactType = 'Email'
  );

## Query 4: Customers Without Addresses
  SELECT C.CustomerID, C.FirstName, C.LastName
  FROM Customer C
  LEFT JOIN Address A ON C.CustomerID = A.CustomerID
  WHERE A.CustomerID IS NULL;

## Query 5: User Login Access
  SELECT 
      UL.Username,
      SG.GroupName AS ApplicationAccess
  FROM UserLogin UL
  JOIN UserLoginSecurityGroup ULSG ON UL.LoginID = ULSG.LoginID
  JOIN SecurityGroup SG ON ULSG.SecurityGroupID = SG.SecurityGroupID;


## Query 6: Customers with Both Billing and Shipping Addresses
  SELECT DISTINCT C.CustomerID, C.FirstName, C.LastName
  FROM Customer C
  JOIN Address A ON C.CustomerID = A.CustomerID
  WHERE A.Purpose IN ('Billing', 'Shipping')
  GROUP BY C.CustomerID, C.FirstName, C.LastName
  HAVING COUNT(DISTINCT A.Purpose) = 2;


## Query 7: Total Number of Customers per City
  SELECT 
      A.City,
      COUNT(C.CustomerID) AS TotalCustomers
  FROM Customer C
  JOIN Address A ON C.CustomerID = A.CustomerID
  GROUP BY A.City;


## Query 8: Find Customers with Expired Contact Mechanisms
  -- Assuming there's an 'ExpirationDate' field in ContactInfo that indicates if the contact mechanism is expired
  SELECT C.CustomerID, C.FirstName, C.LastName, CI.ContactValue, CI.ExpirationDate
  FROM Customer C
  JOIN ContactInfo CI ON C.CustomerID = CI.CustomerID
  WHERE CI.ExpirationDate < CURRENT_DATE;


## Query 9: Payment Methods and Billing Addresses
  SELECT 
      PI.CustomerID,
      PI.PaymentType,
      PI.CardNumber,
      PI.ExpirationDate,
      A.StreetAddress,
      A.City,
      A.StateProvince,
      A.PostalCode,
      A.Country
  FROM PaymentInformation PI
  JOIN Address A ON PI.BillingAddressID = A.AddressID;

## Query 10: Customer Login and Role Information
  SELECT 
      C.CustomerID,
      C.FirstName,
      C.LastName,
      UL.Username,
      UL.Password,
      C.Roles
  FROM Customer C
  JOIN UserLogin UL ON C.CustomerID = UL.CustomerID;

