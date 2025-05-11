# ACS_with_two-factor_authentication

## 📋 Table of Contents
1. [Project Description](#-project-description)
2. [Main Components](#%EF%B8%8F-main-components)
   - [Controller: Arduino Uno + RFID RC522](#-1-controller-arduino-uno--rfid-rc522)
   - [Server Side: Python + Flask](#-2-server-side-python--flask)
   - [Biometrics: Face Recognition](#-3-biometrics-face-recognition-face-recognition)
   - [Database](#%EF%B8%8F-4-database-mysql-or-postresql)
   - [Web Interface](#-5-web-interface-html--css)
   - [Camera](#-6-camera-usb-or-ip-camera)
   - [Real-time Communication](#-7-flask-socketio-websocket)
   - [Additional Hardware](#-8-additional-hardware-components)
3. [RFID Identification](#1%EF%B8%8F⃣-the-principle-of-operation-of-rfid-identification)
4. [Face Recognition System](#2%EF%B8%8F⃣-the-principle-of-operation-of-the-face-recognition-system)
5. [Biometric Authentication Stages](#-the-stages-of-the-biometric-authentication-system)
   - [Face Capture](#-1-capturing-a-users-face-image)
   - [Embedding Comparison](#-2-comparison-of-embaddings)
   - [Similarity Estimation](#-3-an-estimate-of-the-degree-of-similarity-is-the-euclidean-distance)
   - [Missing Biometric Data](#-4-lack-of-biometric-data-in-the-database)
   - [ID Mismatch](#-5-id-mismatch-during-recognition)
   - [6. Successful Authentication](#-6-successful-two-factor-authentication-and-data-transfer)
6. [Security Features](#-security)
   - [Event logging](#-1-full-event-logging)
   - [Two factor authentication](#-2-two-factor-authentication-2fa)
   - [Limited access](#-3-limited-access-to-the-control-panel)
   - [Network security](#-4-network-security-measures)
8. [Future Plans](#-plans-for-the-future)
   - [Integration with the telegram bot](#-integration-with-the-telegram-bot)
   - [Reporting and analytics](#-enhanced-reporting-and-analytics)
   - [Support for multiple biometric factors](#-support-for-multiple-biometric-factors)
   - [Enabling the mobile app](#-enabling-the-mobile-app)
10. [Conclusion](#-conclusion)
11. [Authors](#authors)


## 🔐 Project description

This project is an access control and management system (ACS) implemented using two-factor authentication (2FA). The aim of the project is to ensure reliable identification and control of employees' access to the protected territory of an enterprise, educational institution or other facility with high security requirements.

Unlike traditional ACS based on only one factor (for example, an RFID card), this system implements double identity verification.:

1 What the user has is an RFID card (a unique identifier).

2 Who the user is is a face that is recognized through the camera in real time.

This approach significantly reduces the risk of unauthorized access if the card is lost or tampered with, as it requires both a physical identifier and a match of biometric data.

Key Features:

✅ Two-factor authentication: RFID + face recognition

📸 Real-time video processing for face identification

🌐 A web interface for event monitoring, authorization, and user management

🔄 Real-time information display (using WebSocket / Flask-SocketIO)

🧾 Event log with the ability to filter by users, date and time

🔒 A local database (MySQL) for storing information about users, access rights, and logs

Possible usage scenarios:

- Accounting of employees in organizations and institutions

- Automatic access to the office, laboratory, auditorium

- Secure control at the entrance to dormitories, conference rooms, data centers

The system can be deployed on a single device (for example, a Raspberry Pi with a camera), or in a client-server architecture with a centralized database and remote administration.

##  ⚙️ Main components

### 🧠 1. Controller: Arduino Uno + RFID RC522
The Arduino Uno microcontroller is responsible for interacting with the RC522 RFID reader. When an RFID card is presented, the reader transmits a unique identifier (UID) to the Arduino, which then sends it to the server part of the system. This stage implements the first authentication factor, which is checking whether the user has a physical identifier. Upon successful verification on the server side, the second stage is initiated — biometrics.

## 🧠 2. Server side: Python + Flask
A web server written in Python using Flask manages all the logical components of the system. It receives data from Arduino, processes the results of biometric verification, logs events in a database and displays them via a web interface. An administrator authorization system and the ability to remotely manage users are also implemented.

### 🧠 3. Biometrics: Face Recognition (Face Recognition)
The system uses the face_recognition library (based on dlib) in conjunction with OpenCV to capture and analyze facial images. The comparison is performed by comparing the embeddings of the current image with the reference image. A successful match confirms the user's identity and completes the second authentication stage. If there is a discrepancy, access is blocked.

### 🗂️ 4. Database: MySQL or PostreSQL
A relational database is used to store information about users (full name, UID, RFID, photo path), as well as logs of logins to the system. A table of administrator accounts with password hashing is also implemented. The database provides reliable storage and the possibility of subsequent analysis of events.

### 🌐 5. Web Interface: HTML + CSS 
The admin panel allows you to view current events, manage users, upload new photos, filter logs, and track who is currently in the building. The interface is designed using Flask HTML templates and supports authorization.

### 📷 6. Camera (USB or IP Camera)
The camera connects to the server and is used to capture the user's face when trying to log in. The snapshot is analyzed in real time, without the need to save the image to disk (if not required). A standard USB camera or IP camera is used.

### 🔄 7. Flask-SocketIO (WebSocket)
WebSocket is used via the Flask-SocketIO library to display data on a web page in real time. After successful authentication, the user's data (full name, login time, status) is immediately displayed on the monitoring page without restarting.

### 🔌 8. Additional hardware components
Additional devices can be added to the system, such as an electromagnetic lock or relay for physical control of the passage, a buzzer and LEDs for visual/audible indication of the result, as well as a display to display the current status or user name at the passage site.

## 1️⃣ The principle of operation of RFID identification

RFID (Radio Frequency Identification) is a technology for contactless identification of objects using radio frequencies. This system uses a passive RFID card in the Mifare Classic format, and the reading takes place through the RC522 module connected to the Arduino Uno controller.

![image](https://github.com/user-attachments/assets/ef8e31e1-990c-4269-b5a7-54ea1b89c1e9)

Initially, the user applies the RFID card and reads it. When using readers with an LCD display, in case of a failed readout, a message is displayed to the user asking them to re-insert the card, and when using readers without a display, an audible signal is given informing them of the need to try again. When the card is successfully read and the UID is found in the database, the system receives the user's ID value based on his UID. The Status value determines the type of the activated reader: value 1 corresponds to the input group, value 0 corresponds to the output group. Next, the ID and Status are stored in the session, and upon successful biometric authentication, they are transmitted to the system and/or the Keycloack SERVER.

If the ID is missing from the database, the user must contact the system administrator to enter it in the database, after which the user will be able to pass RFID identification.

## 2️⃣ The principle of operation of the face recognition system

After successful RFID verification, the system initiates the second authentication factor — user's face recognition. This process is performed on the server using the face_recognition library (based on dlib).

![image](https://github.com/user-attachments/assets/c92b4ac9-992a-4093-a2a1-65e9fdb90f2d)

## 📊 The stages of the biometric authentication system

### 📸 1 Capturing a user's face image

After passing through the first phase of two—factor authentication - identification using an RFID card — the process of capturing a face image using an attached camera is started. A video capture is performed within 5 seconds, during which the user must position the face in the center of the frame. This time interval is necessary for:

- getting the best possible face angle,

- compensating for possible webcam delays,

- eliminate the effects of short-term noise or poor lighting.

After the capture is completed, an embedding (feature vector) is extracted from the image, which is a numerical representation of the unique characteristics of the user's face.

An example of the extracted embedding is shown:

![image](https://github.com/user-attachments/assets/1933f07a-2c1a-4177-829e-7bc56f7a80cc)

### 🔍 2 Comparison of embaddings

For authentication, the received embedding is compared with the reference embedding of the user, previously saved in the database. In this case, the system extracts the ID of the user who has passed RFID authentication from the current session, and based on it finds the corresponding biometric template.

### ➖ 3 An estimate of the degree of similarity is the Euclidean distance

The comparison of two embaddings is carried out by calculating the Euclidean distance between them using the formula:

<p align="center">
  <img src="https://github.com/user-attachments/assets/6ebff877-5a86-43ce-9e0f-bb55114d3244" alt="image" />
</p>

If the distance value is less than the threshold value, which in the current implementation is 0.6, then recognition is considered successful. Otherwise, it will be unsuccessful.
​
If the result is unsuccessful, the system automatically initiates a second recognition attempt. This avoids false alarms caused by temporary external interference, such as poor lighting or user movement at the time of capture.

The threshold value can be adjusted:

- reducing the value (for example, to 0.4) improves accuracy, but increases the number of false failures.;

- increasing the value (for example, to 0.8) reduces sensitivity, but may allow for more errors of the first kind.

The optimal value is selected experimentally, depending on the operating conditions.

### ❓ 4 Lack of biometric data in the database

If there is no saved embedding of the person in the database for the user who has passed RFID identification, the system informs about the need to contact the system administrator to register and add a biometric template.

### 🚫 5 ID mismatch during recognition

If recognition is successful, but if the ID stored in the session (from RFID) and the ID of the recognized person do not match, the system blocks access. This prevents the possibility of using other people's RFID cards and violating the two-factor authentication procedure. Next, a return to the RFID identification stage is initiated.

### ✅📤 6 Successful two-factor authentication and data transfer

If the ID matches and the recognition result is positive, the system:

- records the fact of successful authentication,

- initiates sending the following data to the ACS dashboard:

  - User's full name,

  - action status (input or output),
  
  - current date and time.

At the same time, the system sends:

- User ID,

- the status of the action (1 — entry, 0 — exit) - into the centralized access control system — Keycloak, providing consistent authorization within the information infrastructure.

Thus, the face recognition access control and management system developed as part of the thesis implements two-factor authentication, which significantly increases the security level of the facility. The use of biometric features coupled with RFID cards allows you to prevent unauthorized access and effectively identify users. The use of modern algorithms and libraries for face recognition ensures high accuracy and reliability of the system in real time.

## 🔐 Security

The system has been developed taking into account modern requirements for information security and physical access control. The following measures have been implemented:

### 📜📝 1 Full event logging.

All user actions, including login attempts, successful and unsuccessful authentications, as well as administrator actions, are recorded in a database with accurate time and metadata. This allows you to conduct an audit and investigation if necessary.

### 🔐📲 2 Two-factor authentication (2FA).

Entrance to the building is possible only if two factors coincide: the presence of an RFID card (which confirms ownership) and facial recognition (which confirms identity). This eliminates the possibility of logging in using a lost or transferred card.

### 🚪🔒 3 Limited access to the control panel.

The web interface for administration is available only after authorization. Administrator passwords are stored in the database in encrypted form (hashed using a reliable algorithm, such as bcrypt). All user and log management functions are available only to authorized persons.

### 🌐🔐 4 Network security measures.

If necessary, the system can be deployed on an enterprise's internal network or isolated via a VPN, and data transmission is protected by SSL certificates.

## ✅ Plans for the future

The system is designed to be scalable and modernizable. The following improvements are planned to be implemented in the upcoming versions:

### 🤖📱 Integration with the Telegram bot.

With each successful or unsuccessful login, the Telegram bot will send a notification to the administrator or the person responsible for security. The messages will contain the user's name, login time, biometrics result, and, if necessary— a photo of the person at the time of verification. This will allow you to quickly respond to incidents.

### 📊📈 Enhanced reporting and analytics.

It is planned to add the generation of reports for a day, week or month, with the ability to export to PDF or Excel. Administrators will be able to see login statistics, the time of each employee's presence, and identify suspicious activity (for example, repeated unsuccessful login attempts).

### 🧠🔑 Support for multiple biometric factors.

In the future, the system will support additional identification methods.:

🖐️ Fingerprint recognition — by connecting an external scanner or module that allows user identification with high accuracy.

The use of multibiometry (for example, face + fingerprint or face) will improve the reliability of identification and adapt the system to the specific requirements of enterprises with an increased level of protection.

### 📱🔓 Enabling the mobile app.

In the future, it is planned to develop a mobile application for iOS and Android that will allow users to authenticate using a smartphone. The application will be integrated with the ACS system, providing the possibility of using both RFID cards and biometric authentication through the camera of a mobile device. Users will be able to receive notifications about their entries, as well as monitor the system status and accesses via the mobile interface.

## 📘 Conclusion

An access control system with two-factor authentication using RFID and facial recognition significantly increases the level of security at facilities with high security requirements. The combined use of physical (RFID) and biometric (face recognition) authentication factors reduces the risks of unauthorized access, even if one of the factors has been lost or tampered with. This system provides reliable identification and access control, creating additional barriers for intruders.

In addition, the system has a flexible and scalable architecture, which allows it to be adapted for both small offices and large organizations with distributed infrastructure. In addition, the implementation of WebSocket for displaying real-time data and the ability to integrate with other access control systems make the project a convenient and effective tool for monitoring and managing security.

# Authors
If you have any questions, you can ask them to us by writing to us at email:
- ivanovvvvvvvanton3829@gmail.com
