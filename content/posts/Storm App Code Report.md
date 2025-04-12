---
date: 2025-04-12
title: 'StormApp Code'
draft: false
---


**Swift Code Analysis Report: Storm App BLE Sensor Manager and SwiftUI Interface**

The report will provide a detailed analysis of the Swift code for the Storm App. The Application is designed to manage Bluetooth Low Energy (BLE) sensor operations and to offer interactive user interface through SwiftUI. The code is developed in Swift using Xcode, which serves as the integrated development environment (IDE) that facilitates building, debugging, and testing iOS applications. 
The report will provide a detailed analysis of the Swift code for the Storm App. The Application is designed to manage Bluetooth Low Energy (BLE) sensor operations and to offer interactive user interface through SwiftUI. The code is developed in Swift using Xcode, which serves as the integrated development environment (IDE) that facilitates building, debugging, and testing iOS applications. 

**Executive Summary**  

	The app’s BLEManger class handles BLE operations-scanning, connecting data reception, and disconnection. Meanwhile, several SwiftUI views provide user interaction. The app structure follows an even-drive design using delegate callbacks for BLE events instead of a traditional loop, which is similar to how Arduino projects use the setup() and loop() functions. Additionally, the code demonstrates a clear separation of concerns where the BLE communication and UI logic are managed independently, and the entry point is defined in the MyApp struct with a WindowGroup that can be extended to run in separate windows if needed.  

**Swift and Xcode Relationship**

Swift

An expressive programming language used to develop iOS applications. 

Xcode

	Apples IDE for Swift development, which provides tools for UI design (with SwfitUI), debugging, testing, and analysis of performance. Xcode’s Secen management (WindowGroup) enables developers to manage multiple windows and screens.   
**Code Structure Overview**

BLE Sensor Manager (BLEManager Class)

The BLE Sensor Manger manages all aspects of BLE communications including scanning, connecting, service discovery, and data handling. Lastly, the manager contains utility methods for sending commands and copying data to the clipboard.

SwiftUI Views for User Interaction

- *BackupRequestView:* Enables users to request backup data, shows connection status, and provides copy/disconnect functionalities.   
    
- *ActiveRecieverView:* Displays live log data and offers a copy-to-clipboard feature.  
    
- *ContentView:* Acts as the main navigation hub, linking to the other views and initiating BLE scanning upon appearance. 

App Entry Point (MyApp)

App Entry Point is defined using the @main attribute, and sets up the application environment using a WindowGroup, which can be configured to open in separate windows as needed.

**Detailed Code Analysis**

BLE Sensor Manager (BLEManger Class)

The BLEManger class is the heart of the app's BLE functionality. The manager is responsible for initializing BLE, managing connections, discovering services/characteristics, and handling data updates. 

Imports and Class Declaration

**import** SwiftUI  
**import** CoreBluetooth  
**import** UIKit   // For UIPasteboard

Purpose: 

- SwiftUI: Builds the user interface  
- CoreBluetooth: Manges BLE communications  
- UIKit: Provides clipboard functionality

Analogy to Arduino: 

- Similar to including libraries  
    
  




BLE Sensor Manager \- The BLEManager Class

The BLEManger class is responsible for managing all BLE-related operations. It extends NCObject and conforms to multiple protocols.

**class** BleManager: NSObject, ObservableObject, CBCentralManagerDelegate, CBPeripheralDelegate {

Class Declaration: 

- BLEManager is a subclass of NSObject so it can work with Objective-C runtime features. It conforms to ObservableObject, which allows its published properties to be observed by SwiftUI views and update the UI automatically. Lastly, it also conforms to CBCentralMangerDelegate and CBPeripheral so it can handle BLE events like scanning, connection, and receiving data.


Arduino Analogy: 

- Like writing a custom class in an Arduino sketch that wraps BLE functions, where you might define a class to handle all BLE operations with callbacks for events.

Published Properties

  @Published **var** backupData: String?

Declares an optional string property to hold backup log data. The @Published attribute means any change to this property will update any SwiftUI view observing it.

Arduino Analogy:

- Similar to defining a global variable (String backupData) whose change triggers an update on a display or sends data over Serial.

@Published **var** liveLogData: String?  // For live measurement log data (Pipe Port Point Val)

Declares an optional string for storing live log data received from the BLE sensor. Again, marked with @Published to allow automatic UI refresh.

Arduino Analogy: 

- Just like having a variable for live sensor readings that, when updated in the loop, refreshes an LCD or OLED display.

    @Published **var** connectionStatus: String \= "disconnected"

Declares a sting that represents the BLE connection status, starting with the value “disconnected”

Arduino Analogy: 

- Comparable to a status flag variabel (bool isConnected \= false;) used to indicate whether a sensor is connected and to update an LED or serial monitor accordingly.


  
BLE Components and UUIDs

    **var** centralManager: CBCentralManager\!

Declares a variable to hold the CBCentralManager instance that manages scanning and connecting to BLE devices.

Arduino Analogy: 

Similar to defining an instance of a BLE controller object (BLECentral bleCentral;) that handles scanning for devices.

    **var** peripheralDevice: CBPeripheral?

Declares an optional variable for storing the connected BLE peripheral. 

Arduino Analogy: 

- Like having a pointer or reference to a specific BLE module that your’re communicating with after scanning.

**var** sensorCharacteristic: CBCharacteristic?

Holds an optional reference to the BLE characteristic used for live sensor measurements.

Arduino Analogy: 

- This is similar to storing the identifier of a sensor reading parameter that you will use to read live data from a BLE device.

    **var** backupCharacteristic: CBCharacteristic?

Holds an optional reference to the BLE characteristic used for receiving backup log data

Arduino Analogy: 

- Like keeping track of another sensor parameter (or a different data channel) that holds historical or backup data.

 **let** serviceUUID \= CBUUID(string: "4fafc201-1fb5-459e-8fcc-c5c9c331914b")

Defines a constant UUID that identifies the BLE service the app is interested in

Arduino Analogy: 

- Just like defining a constant (\#define SERVICE\_UUID “4fafc201-1fb5-459e-8fcc-c5c9c331914b”) to specify the target service for your BLE communication. 

    **let** sensorCharUUID \= CBUUID(string: "1c95d5e3-d8f7-413a-bf3d-7a2e5d7be87e")  

Defines a constant UUID for the sensor characteristic that provides live measurements.

Arduino Analogy: 

- Similar to defining a constant for the BLE characteristic that handles live sensor data, like a unique ID for the measurement channel.

    **let** backupCharUUID \= CBUUID(string: "beb5483e-36e1-4688-b7f5-ea07361b26a8") 

Defines a constant UUID for the backup characteristic used for backup log data.

Arduino Analogy: 

- Like specifying another constant for a different sensor data channel (for backup or historical data) in an Arduino sketch.

Initialization 

    **override** **init**() {  
        **super**.init()  
        connectionStatus \= "initializing..."  
        centralManager \= CBCentralManager(delegate: **self**, queue: **nil**)  
    }

Explanation: 

- Starts the custom initializer override init() {  
- Calls the superclass initializer (required when subclassing NSObject) super.init()  
- Sets the initial connection status connectionStatus \= “initializing. . .”  
- Creates the central manger with the current instance (self) as its delegate, using the main thread (since the queue is nil) centralManger \= CBCentralManager(delegate: self, queue: nil)

Arduino Analogy: 

- This is similar to the setup() function in Arduino., where you initialize your BLE module and set its callback functions. For example, you might call BLE.begin() and assign function pointers for event handling. 

BLE Lifecycle Callbacks

Checking Bluetooth State:

   **func** centralManagerDidUpdateState(\_ central: CBCentralManager) {  
        **if** central.state \== .poweredOn {  
            connectionStatus \= "scanning..."  
            centralManager.scanForPeripherals(withServices: \[serviceUUID\], options: **nil**)  
        } **else** {  
            connectionStatus \= "bluetooth off"  
        }  
    }

Explanation: 

- A delegate method called when the central manager's state changes. Func centralManagerDidUpdateState(\_ central:CBCentralManager){  
- Check if Bluetooth is turned on. If central.state \== .poweredOn {  
- Updates the status to indicate scanning is beginning.  connectionStatus \= “scanning. . .”  
- Starts scanning for peripherals that provide the specified service. centralManager.scanForPeripherials(withServices:\[serviceUUID\], options: nil)  
- Sets the status if Bluetooth isn't available. Else { connectionStatus \= “bluetooth off” }

Arduino Analogy: 

- Similar to checking if the BLE module is powered up in your Arduino setup() (checking a status flag) before starting a device scan using a function like BLE.scan(). 

Discovering a Peripheral: 

     **func** centralManager(\_ central: CBCentralManager, didDiscover peripheral: CBPeripheral,  
                        advertisementData: \[String: **Any**\], rssi RSSI: NSNumber) {  
        peripheralDevice \= peripheral  
        connectionStatus \= "connecting..."  
        centralManager.stopScan()  
        peripheral.delegate \= **self**  
        centralManager.connect(peripheral, options: **nil**)  
    }

Explanation: 

- Called when a peripheral matching the service criteria is found. Func centralManager(. . . didDiscover peripheral: CBPeripheral, . . .) {  
- Stores the discovered peripheral peripherialDevice \= peripherials  
- Updates status to indicate a connection attempt. connectionStatus \= “connecting. . .”  
- Stops further scanning to avoid multiple connections. centralManager.stopScan()  
- Sets this instance as the delegate for peripheral-specific events. Peripheral.delegate \= self  
- Initiates the connection to the peripheral. centralmanger.connect(peripherial, options: nil)




Arduino Analogy: 

- Much like detecting a BLE device in an Arduino scan loop, then selecting that device, stopping the scan, and calling a function like BLE.connect(device) with appropriate callbacks set up. 

Handling a Successful Connection: 

    **func** centralManager(\_ central: CBCentralManager, didConnect peripheral: CBPeripheral) {  
        connectionStatus \= "connected"  
        peripheral.discoverServices(\[serviceUUID\])  
    }  
    

Explanation: 

- Called when the connection to a peripheral is successful. Func centralmanger(. . . didConnect peripheral: CBPeripherial) {  
- Updates the connection status. connection Status \= “connected”  
- Initiates service discovery on the connected peripheral. peripheral.discoverServies(\[serviceUUID\])

Arduino Analogy: 

- Similar to when an Arduino successfully connects to a BLE sensor and then calls a function to retrieve the available services (BLE.discoverServices()).

Handling Disconnection: 

    **func** centralManager(\_ central: CBCentralManager, didDisconnectPeripheral peripheral: CBPeripheral,  
                        error: Error?) {  
        connectionStatus \= "disconnected"  
        backupData \= **nil**   // Clear backup data on disconnect  
        liveLogData \= **nil**  // Clear live log data on disconnect  
        centralManager.scanForPeripherals(withServices: \[serviceUUID\], options: **nil**)  
    }  
    

Explanation: 

- Called when the peripheral disconnects. Func centralManager(. . . didDisconnectPeripherialperipheral: CBPeripheral, . . .) {  
- Updates the status to reflect the lost connection. connectionStatus \= “disconnected”   
- Clears any stored data. backupData \= nil and liveLogData \= nil  
- Restarts scanning for peripherals. centralManager.scanForPeripherials(withServies:\[serviceUUIS\], options: nil)

Arduino Analogy: 

- Analogous to detecting a loss of connection in an Arduino loop, clearing any displayed data, and restating the scan procedure. 

Service and Characteristics Discovery 

Discovering Services: 

    **func** peripheral(\_ peripheral: CBPeripheral, didDiscoverServices error: Error?) {  
        **if** **let** services \= peripheral.services {  
            **for** service **in** services {  
                // Discover both characteristics on the service.  
                peripheral.discoverCharacteristics(\[sensorCharUUID, backupCharUUID\], for: service)  
            }  
        }  
    }

Explanation: 

- Called when the peripheral services are discovered. Func peripheral(. . . didDiscoverSerives error: Error?) {  
- Safely unwraps the list of services. If let servies \= peripherial.services {   
- Iterates over each discovery service. For service in services {  
- Starts discovery of both the sensor and backup characteristics for each service. Peripheral.discoverCharacteristics(\[sensroCharUUID, backupCharUUID, for: service) 

Arduino Analogy: 

- Like iterating over detected service objects in an Arduino BLE library, then calling a function to retrieve characteristics (device.discoverCharacterisitcs()) for each service.

Discovering Characteristics; 

  **func** peripheral(\_ peripheral: CBPeripheral, didDiscoverCharacteristicsFor service: CBService,  
                    error: Error?) {  
        **if** **let** characteristics \= service.characteristics {  
            **for** char **in** characteristics {  
                **if** char.uuid \== sensorCharUUID {  
                    sensorCharacteristic \= char  
                    peripheral.setNotifyValue(**true**, for: char)  
                } **else** **if** char.uuid \== backupCharUUID {  
                    backupCharacteristic \= char  
                    peripheral.setNotifyValue(**true**, for: char)  
                }  
            }  
        }  
    }  
    

Explanation: 

- Called when characteristics for a given service are discovered. Func peripheral(. . . didDiscoverCharacterisitcsForserivce: CBService, error: Error?) {  
- Unwraps the characteristics array. If let characteristics \= service.characterisitcs {  
- Iterates over each characteristic. For char in characteristics {  
- Checks of the current characteristic matches the sensor UUID. if char,uuid \== sensorCharUUID {   
- Saves the sensor characteristic. sensor Charactertistic \= char  
- Enables notifications so that updates are sent automatically. Peripheral.setNotifyValue(true, for: char)  
- Checks for the backup characteristic. Else if char.uuid \== backupCharUUID {  
- Saves the backup characteristic. backupCharacterisitc \= char  
- Also enables notifications for backup data. Peripheral.setNotifyValue(true, for: char) 

Arduino Analogy: 

- Similar to setting up event callbacks for each sensor data channel on an Arduino; when a new reading is available, an interrupt or callback updates a variable.


  
Data Handling in Callbacks

    **func** peripheral(\_ peripheral: CBPeripheral, didUpdateValueFor characteristic: CBCharacteristic,  
                    error: Error?) {  
        **if** **let** data \= characteristic.value, **let** receivedString \= String(data: data, encoding: .utf8) {  
            print("Received raw data from \\(characteristic.uuid): \\(receivedString)")  
            DispatchQueue.main.async {  
                // Ignore an echo of the GET\_LOG command.  
                **if** receivedString \== "GET\_LOG" { **return** }  
                **if** characteristic.uuid \== **self**.backupCharUUID {  
                    **if** receivedString.hasPrefix("LOG Values: ") {  
                        **self**.backupData \= String(receivedString.dropFirst("LOG Values: ".count))  
                    } **else** **if** receivedString.hasPrefix("LIVE LOG: ") {  
                        **let** liveMsg \= String(receivedString.dropFirst("LIVE LOG: ".count))  
                        **if** **let** existing \= **self**.liveLogData, \!existing.isEmpty {  
                            **self**.liveLogData \= existing \+ "\\n" \+ liveMsg  
                        } **else** {  
                            **self**.liveLogData \= liveMsg  
                        }  
                    } **else** {  
                        // Fallback: update backup data.  
                        **self**.backupData \= receivedString  
                    }  
                } **else** **if** characteristic.uuid \== **self**.sensorCharUUID {  
                    print("Live data (VH): \\(receivedString)")  
                }  
            }  
        }  
    }

Explanation: 

- Called when new data is received from a characteristic. Func peripheral(. . . didUpdateValueFor characteristic: CBCharacteristic, error: Error?) {  
- Unwraps the raw data and converts it to a UTF-8 string. If let data \= characteristic.value, let receivedString \= String(data:  data,  encoding: .utf8) {  
- Logs the received data. Print (“received raw data from \\(characteristic.uuid): \\(recievedString)”)  
- Dispatches the UI update to the main thread. DispatchQueue.main.async { . . . }  
- Inside the closure:   
  - Skips processing if the data is an echo of the command. If recievedString \== “GET\_LOG” { return }  
  - Checks if the data comes from the backup characteristic. If characteristic.uuid \== self.backupCharUUID {  
    - Removes the prefix and updates backupData. If recievedString.hasP{refix(“LOG Values: “) { . . . }  
    - Processes live log data by appending or setting it. Else if receivedString.hasPrefix(“LIVE LOG: “) { . . . }  
    - Uses the received string as a fallback. Else { self.backupData \= receivedString }  
  - For Sensor characteristic data, logs the live measurement. Else if characteristic.uuid \== self.sensroCharUUID {

Arduino Analogy: 

- Just like reading serial data in Arduino, where you parse incoming bytes (using functions like Serial.readString()) and then decide whether to update a display or log data depending on the received command prefix.

Command and Utility Functions

    // Send the GET\_LOG command via the backup characteristic.  
    **func** requestBackupData() {  
        **guard** **let** peripheral \= peripheralDevice, **let** backupChar \= backupCharacteristic **else** {  
            connectionStatus \= "not connected"  
            **return**  
        }  
        backupData \= **nil**  // Clear old backup data  
        **let** command \= "GET\_LOG".data(using: .utf8)\!  
        peripheral.writeValue(command, for: backupChar, type: .withResponse)  
    }  
    

Explanation: 

- Defines a method to send a command to request backup Data. func requestBackupData() {  
- Checks that both peripheral and backup characteristics are available; otherwise, sets the states to “not connected”  and exits. Guard let peripheral \= peripheralDevice, let backupChar \= backupCharacteristics else { . . . }   
- Clears any previously stored backup data. backupData \= nil  
- Converts the string “GET\_LOG” into data (UTF-8 encoded). Let command \= “GET\_LOG”.data(using: .utf8)\!  
- Writes the command data to the backup characteristic and expects a response. Peripheral.writeValue(command, for: backupChar, type: .withResponse)

Arduino Analogy: 

Similar to sending a command over a Serial connection in Arduino (Serial.println(“GET\_LOG”)l) to instruct a sensor to send stored log data. 

Disconnecting the Peripheral: 

    // Disconnect the peripheral and clear the data.  
    **func** disconnect() {  
        **if** **let** peripheral \= peripheralDevice {  
            centralManager.cancelPeripheralConnection(peripheral)  
        }  
        backupData \= **nil**  
        liveLogData \= **nil**  
        connectionStatus \= "disconnected"  
    }  
    

Explanation: 

- Defines a method to disconnect the BLE peripheral. Func disconnect() {  
- Check if there is a connected peripheral. If let peripheral \= peripheralDevice { . . . }  
- Cancels the connection with the peripheral. centralManager.cancelPeripheralConnection(peripheral)  
- Clears any stored data. backupData \= nil and liveLogData \= nil  
- Updates the status. connectionStatus \= “disconnected”

Arduino Analogy:

- Like calling a function in Arduino disconnect or reset teh BLE module and then clearing any stored sensor values or display messages.


  
Clipboard Functions: 

    // Copy the current backup data to the clipboard.  
    **func** copyBackupDataToClipboard() {  
        **if** **let** dataToCopy \= backupData, \!dataToCopy.isEmpty {  
            UIPasteboard.general.string \= dataToCopy  
            print("Backup data copied to clipboard.")  
        } **else** {  
            print("No backup data to copy.")  
        }  
    }

Explanation: 

- Declares a method to copy the backup data to the system clipboard. Func copyBackupDataToClipboard() {  
- Checks if there is valid backup data. If let dataToCopy \= backupData, \!dataToCopy.isEmpty {  
- Copies the data to the clipboard. UIPasteboard.general.string \= dataToCopy  
- Logs a confirmation. print(“Backup data copied to clipboard.”)  
- Logs a message if there's no data. Else { print(“No backup data to copy.”) }

Arduino Analogy: 

- While Arduino does not have a clipboard, this is similar to sending data over Serial for debugging or saving data to an SD card for later retrieval.

    // Copy the current live log data to the clipboard.  
    **func** copyLiveDataToClipboard() {  
        **if** **let** dataToCopy \= liveLogData, \!dataToCopy.isEmpty {  
            UIPasteboard.general.string \= dataToCopy  
            print("Live log data copied to clipboard.")  
        } **else** {  
            print("No live log data to copy.")  
        }  
    }  
}

Explanation: 

- Declares a method to copy live log data to the clipboard. Func copyLiveDataToClipboard() {  
- It checks for valid data, copies it to the clipboard, and logs the outcome. Logic inside this function mirrors the previous function.

Arduino Analogy:

- Again, analogous to outputting live sensor data to a serial monitor or writing it to external storage.

SwiftUI Views for User Interaction

BackupRequestView:

// **MARK: \- Back-Up Request View**  
**struct** BackupRequestView: View {  
    @EnvironmentObject **var** bleManager: BleManager  
      
    **var** body: **some** View {  
        VStack(spacing: 20) {  
            Text("Back-Up Request")  
                .font(.title2)  
                .fontWeight(.bold)

Explanation: 

- Defines a SwiftUI view named BackupRequestView. Struct BackupRequestView: View {  
- Injects the shared BLE manager instance. @EnvironmentObject var bleManager: BleManager  
- Begins the views layout description. Var body: some View {  
- Created a vertical stack with 20 points of spacing between elements. VStack(spacing: 20\) {  
- Displays the title text. Text(“Back-Up Request”)  
- Applies title styling and bold font weight. .font(.title2) and .fontWeight(.bold)

Arduino Analogy: 

- Like setting up a display screen in Arduino and drawing static text for a menu title using a graphics library (e.g., calling functions like display.setTextSize(2) and display.print(“Back-Up Request”))

            Text("Status: \\(bleManager.connectionStatus)")

Explanation: 

- Displays the current BLE connection status by accessing the published property.

Arduino Analogy: 

- Equivalent to printing the status of a sensor connection on an LCD or serial monitor (e.g., Serial.print(“Status: “); Serial.println(status);).

            Button(action: {  
                bleManager.requestBackupData()  
            }) {  
                Text("Request Back-Up Data")  
                    .font(.headline)  
                    .padding()  
                    .frame(maxWidth: .infinity)  
                    .background(Color.orange)  
                    .foregroundColor(.white)  
                    .cornerRadius(10)  
            }

Explanation:

- Create a button that, when pressed, calls the requestBackupData() method. Button(action: { bleManager.requestBackupData() }) { . . . }  
- Inside the buttons label closure:   
  - Set the button text. Text(“Request Back-Up Data”)  
  - Applies a headline font style. .font(.headline)  
  - Adds padding around the text. .padding()  
  - Makes the button expand to fill available horizontal space. .frame(maxWidth: .infinity)  
  - Sets the background color to orange. .background(Color.orange)  
  - Set the text color to white. .foregroundColor(.white)  
  - Rounds the corners of the button. .cornerRadius(10)

Arduino Analogy: 

- Similar to defining a button on a touchscreen in an Arduino project, where pressing the button triggers a function to request data from a sensor.

         **if** **let** backup \= bleManager.backupData, \!backup.isEmpty {  
                ScrollView {  
                    Text(backup)  
                        .padding()  
                        .frame(maxWidth: .infinity, alignment: .leading)  
                        .background(Color.gray.opacity(0.1))  
                        .cornerRadius(8)  
                }  
                .frame(height: 200)  
            } **else** {  
                Text("No Backup Data Received")  
                    .foregroundColor(.gray)  
            }

Explanation: 

- Conditional Bock:   
  Check if there is backup data.  
  If backup data exists and is not empty: if let backup \= bleManager.backupData, \!backup.isEmpty { . . . }  
- Displays the backup data in a scrollable area. ScrollView { . . . }  
- Inside the ScrollView:   
  - Shows the backup data text. Text(backup)  
  - Styles the text container. .padding(), .frame(maxWidth: .infinity, alignment: .leading), .background( . . . ), .cornerRadius(8)  
  - Sets a fixed height for the scrollable view. .frame(height: 200\)  
  - If no data exists, display a placeholder message. Else { Text(“No Backup Data Received”) . . . }

Arduino Analogy: 

- Like checking if sensor data is available and, if so, displaying it on a scrolling text area on an OLED screen; otherwise, displaying “No Data” in gray text.

            // Connect/Disconnect button – when disconnecting, data is cleared.  
            Button(action: {  
                **if** bleManager.connectionStatus.contains("connected") {  
                    bleManager.disconnect()  
                } **else** {  
                    bleManager.centralManager.scanForPeripherals(withServices: \[bleManager.serviceUUID\], options: **nil**)  
                }  
            }) {  
                Text(bleManager.connectionStatus.contains("connected") ? "Disconnect" : "Connect")  
                    .font(.headline)  
                    .padding()  
                    .frame(maxWidth: .infinity)  
                    .background(Color.blue)  
                    .foregroundColor(.white)  
                    .cornerRadius(10)  
            }  
            

Explanation: 

- Creates a button whose action checks the connection status. Button(action: { . . . }) { . . . }  
- Inside the action closure:  
  - Disconnects if connected if connected; otherwise, starts scanning for peripherals. If bleManager.connectionStatus.contains(“connected”) { bleManager.disconnect() } else { . . . }  
- Label of the button:   
  - Uses a ternary operator to set the button text to “Disconnect” if connected, or “Connect” if not.  
  - Applies styling similar to the previous button.

Arduino Analogy: 

- Similar to having a physical button on an Arduino project that toggles between connecting and disconnecting a sensor, updating the display text accordingly.

            // Copy Backup Data button.  
            Button(action: {  
                bleManager.copyBackupDataToClipboard()  
            }) {  
                Text("Copy Backup Data")  
                    .font(.headline)  
                    .padding()  
                    .frame(maxWidth: .infinity)  
                    .background(Color.green)  
                    .foregroundColor(.white)  
                    .cornerRadius(10)  
            }  
            

Explanation: 

- Creates a button that triggers the method to copy backup data to the clipboard. The button is styled with a green background.

Arduino Analogy: 

- Comparable to a button that, when pressed on a touchscreen Arduino device, sends the backup data to a serial monitor or writes it to external storage.

            Spacer()

Explanation: 

- Inserts a flexible space that pushes the content upward.


Arduino Analogy: 

- Like adding blank space on a display to control layout alignment.

        }  
        .padding()  
        .navigationTitle("Back-Up Request")  
    }  
}

Explanation: 

- Closes the VStack. }  
- Adds padding around the entire VStack. .padding()  
- Sets the navigation bar title. .navigationTitle(“Back-Up Request”)

Arduino Analogy: 

- Similar to setting margins or borders on a display screen and labeling the current screen with a title.

ActiveReceiverView: 

// **MARK: \- Active Receiver View**  
**struct** ActiveReceiverView: View {  
    @EnvironmentObject **var** bleManager: BleManager  
      
    **var** body: **some** View {  
        VStack(spacing: 20) {  
            Text("Active Receiver")  
                .font(.title2)  
                .fontWeight(.bold)

Explanation:

- Defines a SwiftUI view for displaying live sensor log data. It starts similarly to BackupRequesView with a title.

Arduino Analogy: 

- Like setting up a separate screen or page on an Arduino touchscreen to display real‑time sensor data.

            Text("Status: \\(bleManager.connectionStatus)")

Explanation: 

- Displays the connection status.

Arduino Analogy: 

- As before, like printing the current connection status to an LCD display.

            **if** **let** liveLog \= bleManager.liveLogData, \!liveLog.isEmpty {  
                ScrollView {  
                    Text(liveLog)  
                        .padding()  
                        .frame(maxWidth: .infinity, alignment: .leading)  
                        .background(Color.gray.opacity(0.1))  
                        .cornerRadius(8)  
                }  
                .frame(height: 200)  
            } **else** {  
                Text("No Live Log Data Received")  
                    .foregroundColor(.gray)  
            }  
            

Explanation: 

- Similar to the backup view, but for live log data. It conditionally displays a scrollable area if live data exists; otherwise, it shows a placeholder.

Arduino Analogy: 

- Like checking for live sensor output and either displaying it on a scrolling text area or showing “No Data” if nothing is received.

           // Copy Live Data button.  
            Button(action: {  
                bleManager.copyLiveDataToClipboard()  
            }) {  
                Text("Copy Live Data")  
                    .font(.headline)  
                    .padding()  
                    .frame(maxWidth: .infinity)  
                    .background(Color.green)  
                    .foregroundColor(.white)  
                    .cornerRadius(10)  
            }

Explanation: 

- Creates a button that copies the live log data to the clipboard, with styling similar to the backup copy button.

Arduino Analogy: 

- Just like a button on an Arduino interface that, when pressed, sends the live data to another output channel (such as Serial).

            Spacer()  
        }  
        .padding()  
        .navigationTitle("Active Receiver")  
    }  
}

Explanation: 

- Completes the layout by adding spacing, padding, and setting the navigation title.

Arduino Analogy: 

- Similar to finalizing the layout of a screen on an Arduino display.

Main ContentView:

// **MARK: \- Main View**  
**struct** ContentView: View {  
    @StateObject **private** **var** bleManager \= BleManager()

Explanation: 

- Declares the main content view. Struct ContentView: View {  
- Creates and owns a single instance of BleManager that persists for the life of this view. @StateObject private var bleManager \= BleManager()

Arduino Analogy: 

- Like initializing a central object or manager for sensor operations in the setup() of an Arduino project.

    **var** body: **some** View {  
        NavigationView {  
            VStack(spacing: 40) {

Explanation: 

- Begins the view body. Var body: someView {  
- Wraps the view in a navigation controller for managing transitions. NavigationView {  
- Creates a vertical stack with 40 points of spacing between items. VStack(spacing: 40\) {

Arduino Analogy: 

- Similar to setting up a main menu screen on an Arduino touchscreen with buttons laid out vertically.

               NavigationLink(destination: BackupRequestView().environmentObject(bleManager)) {  
                    Text("Back-Up Request")  
                        .font(.title)  
                        .padding()  
                        .frame(maxWidth: .infinity)  
                        .background(Color.orange)  
                        .foregroundColor(.white)  
                        .cornerRadius(12)  
                }  
                

Explanation: 

- Creates a navigation link (a clickable element) that routes the user to the BackupRequestView. The BLE manager is passed along via the environment. 

Arduino Analogy:

- Like having a touchscreen button that, when pressed, switches the display to a new screen showing backup data.

                NavigationLink(destination: ActiveReceiverView().environmentObject(bleManager)) {  
                    Text("Active Receiver")  
                        .font(.title)  
                        .padding()  
                        .frame(maxWidth: .infinity)  
                        .background(Color.purple)  
                        .foregroundColor(.white)  
                        .cornerRadius(12)  
                }

Explanation: 

- Creates another navigation link that takes the user to the ActiveRecieverView with the BLE manager passed along.

Arduino Analogy: 

- Like a separate button on a main menu that opens the live data display screen.

            }  
            .navigationTitle("Main Menu")  
            .onAppear {  
                bleManager.centralManager.scanForPeripherals(withServices: \[bleManager.serviceUUID\], options: **nil**)  
            }  
        }  
        .environmentObject(bleManager)  
    }  
}

Explanation: 

- Closes the VStack. }  
- Sets the title for the navigation bar. .navigationTitle(“Main Menu”)  
- Execute code when the view appears-here, it starts scanning for peripherals. .onAppear { . . . }  
- Makes the BLE manager available to all child views. .environementObject(bleManager)

Arduino Analogy: 

- Similar to initializing sensor scanning when the main menu screen loads on an Arduino, and passing the central sensor manager to other parts of the program.

App Entry Point 

**import** SwiftUI  
**@main**  
**struct** MyApp: App {  
    **var** body: **some** Scene {  
        WindowGroup {  
            ContentView()  
        }  
   

Explanation:

- Designates the entry point of the application. @main   
- Declares the main app structure. Struct MyApp: App {  
- Defines the scene of the app. Var body: some Scene {  
- Specifies that contentView is the root view in the app's primary window. WindowGroup { ContentView() }  
- Note on Multiple Windows:  
  - Xcodes WindowGroup can be extended to support multiple windows on platforms like iPadOS and macOS.

Arduino Analogy:

- Comparable to the setup() function in Arduino that initializes all components and then hands control over to the loop(). Here, the “loop” is handled by SwiftUI’s reactive system, and the window is akin to the main display screen that the user interacts with.

**Conclusion**

The report has broken down every line (or logical block) of the Swift code with detailed explanations and direct analogies to Arduino code practices. Each code element—from importing frameworks and setting up BLE communications in the BleManager class to constructing interactive SwiftUI views and configuring the app entry point—has been related to how similar functionality might be implemented in an C++ environment..

