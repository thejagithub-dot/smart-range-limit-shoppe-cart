# smart-range-limit-shopping-cart
#include <Keypad.h>
#include <SoftwareSerial.h>

// RFID Setup
SoftwareSerial mySerial(11, 3);
double total = 0.0;

// Keypad Setup
const byte ROWS = 4;
const byte COLS = 4;
char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

int selectedThreshold = 0;
int lastScannedPrice = 0;

void setup() {
  Serial.begin(9600);
  mySerial.begin(9600);
  Serial.println("EM-18 RFID Reader Ready");
}

void loop() {
  // Keypad handling
  char key = keypad.getKey();
  
  if (key) {
    switch (key) {
      case '1':
        selectedThreshold = 1000;
        Serial.println("Under 1000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '2':
        selectedThreshold = 2000;
        Serial.println("Under 2000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '3':
        selectedThreshold = 3000;
        Serial.println("Under 3000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '4':
        selectedThreshold = 4000;
        Serial.println("Under 4000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '5':
        selectedThreshold = 5000;
        Serial.println("Under 5000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '6':
        selectedThreshold = 6000;
        Serial.println("Under 6000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '7':
        selectedThreshold = 7000;
        Serial.println("Under 7000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '8':
        selectedThreshold = 8000;
        Serial.println("Under 8000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case '9':
        selectedThreshold = 9000;
        Serial.println("Under 9000");
        displayBalance();
        Serial.println("Press 'C' to know the total price.");
        break;
      case 'A':
        if (lastScannedPrice > 0) {
          addOrRemoveProduct(true); // true indicates adding product
        } else {
          Serial.println("No product scanned to add.");
        }
        break;
      case 'B':
        if (lastScannedPrice > 0) {
          addOrRemoveProduct(false); // false indicates removing product
        } else {
          Serial.println("No product scanned to remove.");
        }
        break;
      case 'C':
        Serial.print("Total price: Rs. ");
        Serial.println(total);
        break;
      default:
        Serial.println("Invalid key");
        break;
    }
  }

  // RFID handling
  if (mySerial.available() > 0) {
    String rfidTag = "";
    while (mySerial.available() > 0) {
      char c = mySerial.read();
      if (c != '\n' && c != '\r') {
        rfidTag += c;
      }
      delay(10);
    }
    
    Serial.print("RFID Tag: ");
    Serial.println(rfidTag);

    int productPrice = 0;
    if (rfidTag == "4D00E41B4BF9") {
      productPrice = 1000;
      Serial.println("Product: desktop table | Price: Rs. 1000");
    } else if (rfidTag == "500080E30A39") {
      productPrice = 2000;
      Serial.println("Product: boat earbuds | Price: Rs. 2000");
    } else if (rfidTag == "4D00E3D91562") {
      productPrice = 4500;
      Serial.println("Product: smart watch | Price: Rs. 4500");
    } else if (rfidTag == "4D00E42CC540") {
      productPrice = 6000;
      Serial.println("Product: water purifier | Price: Rs. 6000");
    } else {
      Serial.println("Unknown Product");
    }

    if (productPrice > 0) {
      lastScannedPrice = productPrice;
      // Prompt for adding or removing the product
      Serial.println("Press 'A' to add or 'B' to remove this product.");
    }
  }
}

void addOrRemoveProduct(bool addProduct) {
  if (lastScannedPrice > 0) {
    if (addProduct) {
      if (selectedThreshold > 0 && total + lastScannedPrice > selectedThreshold) {
        Serial.println("Insufficient balance.");
      } else {
        total += lastScannedPrice;
        Serial.println("Product added successfully.");
      }
    } else { // Removing product
      if (total >= lastScannedPrice) {
        total -= lastScannedPrice;
        Serial.println("Product removed successfully.");
      } else {
        Serial.println("Invalid operation. Product price exceeds current total.");
      }
    }
    lastScannedPrice = 0; // Reset after operation
    displayBalance();
  } else {
    Serial.println("No product scanned.");
  }
}

void displayBalance() {
  if (selectedThreshold > 0) {
    double balance = selectedThreshold - total;
    Serial.print("Current balance under selected threshold: Rs. ");
    Serial.println(balance);
  } else {
    Serial.println("No threshold selected.");
  }
}
