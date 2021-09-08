# Arduino_Money_Collector
//Programmed car that moves towards a person that it finds to ask it for some change. 
// The Gold-Digger, our Arduino project, is a robot that searches for humans and approaches them. As soon, as it approaches them, the machine will
//continuously bump into the person until this person inserts a 1$ or a 2$ coin in it. Then, the Gold-Digger will go back, change its trajectory
//and leave the person alone to search for someone else. Overall, the Gold-Digger's function is to track people and collect money from them.
//
//First, the Gold-Digger initializes its environment in order to know what to track, over an interval of 30 seconds. After the setup has been completed and
//printed on the serial monitor, the robot will use its turn function until its "pirdetect" function prints out a value of 1, which means that the motion-detector(Pyroelectric)
//sensor has found a source of Infrared Radiation (which in this case is a human body). Then, it sets the motors to a forward motion which is controlled by Pulse Width Modulation.
//The robot will initially bump into the detected person, and then it will continuously keep bumping by going back and forth between a distance of 10cm and 20cm until
//the person inserts a coin into the slit. The coin will then be detected by another void function called "checkcoin" in which a value of 0 will be printed out if the
//infrared sensor, used to detect a coin, detects a coin. Through multiple movement functions, the Gold-Digger goes back, changes its trajectory goes away to find
//someone else to bother.
 

//define pins




//motor pins on H-bridge

 

const int Aen=5;
const int Ben=10;
const int MA1=6;
const int MA2=7;
const int MB1=8;
const int MB2=9;


//sensor pins

int trig=3;
int echo=2;
int pir=A3;
int coin=13;

//variables
float distance = 0;
int money=1, received=1;
float t1, tf,t=0;
int i=0;

//functions

void checkcoin() //sets 'received' to 0 if a coin has been received
{
     money= digitalRead (coin);
     if (money<1) received = 0;
}


//movement functions

void pause () //stops the robot

{

  digitalWrite (MA1, LOW);
  digitalWrite (MA2, LOW);

  digitalWrite (MB1, LOW);
  digitalWrite (MB2, LOW);
 
  analogWrite (Aen, 0);
  analogWrite (Ben, 0);
 
  }

void turn () {   //turns the robot in small intervals
 
  digitalWrite (MA1, LOW);
  digitalWrite (MA2, LOW);

  digitalWrite (MB1, HIGH);
  digitalWrite (MB2, LOW);


  analogWrite (Aen, 180);
  analogWrite (Ben, 180);
    delay(150);
    pause();
    delay(150);
}



void turnaround ()  

{
 
  digitalWrite (MA1, LOW);
  digitalWrite (MA2, LOW);

  digitalWrite (MB1, HIGH);
  digitalWrite (MB2, LOW);


  analogWrite (Aen, 160);
  analogWrite (Ben, 160);
}


void runforward () //drives the robot straight ahead
{
  digitalWrite (MA1, HIGH);
  digitalWrite (MA2, LOW);

  digitalWrite (MB1, HIGH);
  digitalWrite (MB2, LOW);
 
  analogWrite (Aen, 200);
  analogWrite (Ben, 200);
  }

void goback () //moves the robot straight back

{

  digitalWrite (MA1, LOW);
  digitalWrite (MA2, HIGH);

  digitalWrite (MB1, LOW);
  digitalWrite (MB2, HIGH);
 
  analogWrite (Aen, 100);
  analogWrite (Ben, 100);
 
  }
 
void pirdetect() {  //By using motion-sensor detector
 
   
   while(i==0){        
     turn ();
    i=digitalRead(pir);        // if it detects movement, PIR=1
    Serial.print ("\n PIR=");
    Serial.print (digitalRead(pir));  
 
    }
 
      pause();
    }
   



float measureDistance() {  //ultrasound sensor code
 
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
 
  digitalWrite(trig, LOW);
 
  checkcoin();
 
  return (0.5 * ((pulseIn(echo, HIGH)) / 29));  //sound moves (29us/cm), calculate distance, return distance in cm
}



void setup() {

  Serial.begin (9600);
 
  pinMode(Aen,OUTPUT);
  pinMode(Ben,OUTPUT);
  pinMode(MA1,OUTPUT);
  pinMode(MA2,OUTPUT);
  pinMode(MB1,OUTPUT);
  pinMode(MB2,OUTPUT);
 
  pinMode(trig,OUTPUT);
  pinMode(echo,INPUT);
  pinMode(pir,INPUT);
  pinMode(coin,INPUT);

   pinMode(4,OUTPUT);

   digitalWrite(4,HIGH);
 
  Serial.print ("Setup starting\n");
  delay (3000); //30 s delay for the sensor to stabilize

 pirdetect();

 Serial.print ("Setup complete\n");
}


void loop() {

   
  if (received==1) //if no coin received
  {
     
     distance = measureDistance();
   
    if (distance>20.0)  runforward ();
   
 
    else if(distance <10.0) {
             
            goback();
   
 }
          checkcoin();  //check for coin in every iteration
   
    }
 
   
//
   else if (received ==0) // if a coin has been detected, go back, then turn, then drive away
   
 {
    pause();
    delay (500);
    goback();
    delay (500);
    pause();
    delay (500);
   
    turnaround ();
    delay (1500);

    pause();
    delay (500);
   
    runforward();

    delay (4000);
   
    pause();
   
    delay (120000);//wait 2 minutes before running again
    }
     money= digitalRead (coin);

 Serial.print ("\nIR=");

Serial.print (money);
 Serial.print ("\t PIR=");
 Serial.print (digitalRead(pir));
  distance= measureDistance();
    Serial.print ("\tdistance=");
     Serial.print (distance);

}   
