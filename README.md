# GotYourBack Training Brace
#include <SPI.h>

#include <SD.h>

const int chipSelect = 4;
int piezoPin = 2; 

const int Pmeterpin = A1; 

const int EMGpin1 = A4; 
const int EMGpin2 = A3; 

float EMGmv1 = 0; 
float EMGmv2 = 0; 

int EMG1 = 0; 
int EMG2 = 0;

int Pmeter = 1; 

float Pmeter_adjusted = .1; 

int Count= 0;

void setup() { 

  pinMode(piezoPin, OUTPUT); 

  Serial.begin(115200); 
  while (!Serial){
    ;
  }
 
 Serial.print ("initializing SD card");
  // see if the card is present and can be initialized:
  if (!SD.begin(chipSelect)) {
    Serial.println("Card failed, or not present");
    // don't do anything more:
    while (1);
  }
  Serial.println("card initialized.");

  ;

} 
void loop() {

EMG1 = analogRead(EMGpin1); 
EMG2 = analogRead(EMGpin2); 

  EMGmv1 = ((EMG1 *0.012890625) - 1.65)-5; 
  EMGmv2 = ((EMG2 *0.012890625) - 1.65)-5; 

  Serial.print(EMGmv1); 

  Serial.print(", "); 
  Serial.print(EMGmv2); 

  Serial.print(", "); 


  Pmeter = analogRead(Pmeterpin); 

  Pmeter_adjusted = 0.005*analogRead(Pmeterpin); 

  Serial.println(Pmeter_adjusted); 

//if either emg reading is higher than the threshold play a tone
  if(EMGmv1>=Pmeter_adjusted || EMGmv2>=Pmeter_adjusted){ 

    tone(piezoPin, 1000, 500); 
    
  }
  

  else{ 

    delay(1); 

  } 
Count=Count+1;
delay (5); 
//SD card
// make a string for assembling the data to log:
  String dataString = "";

    dataString += String(Count);
    dataString += " , ";
    dataString += String(EMGmv1);
    dataString += " , ";
    dataString += String(EMGmv2);
    dataString += " , ";
    dataString += String(Pmeter_adjusted);
   
      dataString += ",";
   

  // open the file. note that only one file can be open at a time,
  // so you have to close this one before opening another.
  File dataFile = SD.open("datalog.txt", FILE_WRITE);

  // if the file is available, write to it
  if (dataFile) {
    dataFile.println(dataString);
    dataFile.close();
    // print to the serial port too:
    Serial.println(dataString);
  }
  // if the file isn't open, pop up an error:
  else {
    Serial.println("error opening datalog.txt");
  } 
}

 
 
