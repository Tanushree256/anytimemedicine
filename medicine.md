#include <Key.h>
#include <Keypad.h>
#include<LiquidCrystal.h>

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define Password_Length 5
//char Data[Password_Length];
byte data_count = 0, master_count = 0;  
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
int relay1=40;
int relay2=42;
int relay3=44;
int relay4=46;
int voice=8;
long randomNumber;
char textmessage[60];
String mystr;
void SendSms( char *num1, char * str1 );
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
char CARD_1[]="3A001AB9C158 ";
char CARD_2[]="02107136";  // INSUFFICIENT BALANCE
char CARD_3[]="02107136";
char buff[4];
char Data[Password_Length]; 
char Master[Password_Length] = "1234"; 
//byte data_count = 0, master_count = 0;
bool Pass_is_good;
char customKey=0;
String message="";
char ch;
const byte ROWS = 4;
const byte COLS = 4;

//char hexaKeys[ROWS][COLS] = {
//  {'1', '2', '3', 'A'},
//  {'4', '5', '6', 'B'},
//  {'7', '8', '9', 'C'},
//  {'*', '0', '#', 'D'}
//};

char hexaKeys[ROWS][COLS] = {
  {'0', '1', '2', '3'},
  {'4', '5', '6', '7'},
  {'8', '9', 'A', 'B'},
  {'C', 'D', 'E', '$'}
};

byte rowPins[ROWS] = {22, 24, 26, 28};
byte colPins[COLS] = {30, 32, 34, 36};

Keypad customKeypad = Keypad(makeKeymap(hexaKeys), rowPins, colPins, ROWS, COLS);

int key_num = 0,m=0,l=1,z=0,x=0;
int total_count_tab1 = 20,total_count_tab2 = 20,total_count_tab3 = 20,total_count_tab4 = 20,Tab_temp;
char Tabbuff[20];
char buf11[10],buf22[10],buf33[10],buf44[10];
void RFID_CHECKING();
void prescription();

void DISPATCHING_TABLET1(void);
void DISPATCHING_TABLET2(void);
void DISPATCHING_TABLET3(void);
void DISPATCHING_TABLET4(void);


int total_amount = 1000,amount_temp;

void setup()
{
  pinMode(relay1,OUTPUT); 
  pinMode(relay2,OUTPUT); 
  pinMode(relay3,OUTPUT); 
  pinMode(relay4,OUTPUT); 
  pinMode(voice,OUTPUT);
  Serial.begin(115200);
  Serial2.begin(9600);
  digitalWrite(voice,HIGH);
  lcd.begin(20, 4);
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("      WELCOME        ");
  lcd.setCursor(0,1);
  lcd.print("        TO           ");
  lcd.setCursor(0,2);
  lcd.print("      ATM        ");
  delay(4000);
  lcd.clear();
  
}
void loop()
{
//     customKey=0;
//    lcd.clear();
//    lcd.setCursor(0,0);
//    lcd.print("PLEASE CHOOSE THE ");
//    lcd.setCursor(0,1);
//    lcd.print("MEDICINEs");
//    lcd.setCursor(0,2);
//    lcd.print("1.GENERAL  ");
//    lcd.setCursor(0,3);
//    lcd.print("2.PRESCRIPTION");
//    delay(2000);
//  while(customKey==0) 
//  customKey = customKeypad.getKey();
//  if(customKey=='1')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("GENERAL TABLETS");
//          GENERAL_MEDICINE();
//
//         delay(1000);
//
//  }
// else if(customKey=='2')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("PRESCRIPTION");
//          delay(1000);
          RFID_CHECKING();

  }
     
  
//}
//void GENERAL_MEDICINE()
//{
//    customKey=0;
//    lcd.clear();
//    lcd.setCursor(0,0);
//    lcd.print("PLEASE CHOOSE THE ");
//    lcd.setCursor(0,1);
//    lcd.print("MEDICINE FROM LIST");
//    lcd.setCursor(0,2);
//    lcd.print("1.FEVER       2.COLD");
//    lcd.setCursor(0,3);
//    lcd.print("3.COUGH       4.HEADACHE");
//    delay(2000);
//  while(customKey==0) 
//  customKey = customKeypad.getKey();
//  if(customKey=='1')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("FEVER TABLET SELECTED");
//        //  DISPATCHING_TABLET1();
////          digitalWrite(relay1,HIGH);
//         delay(1000);
//          DISPATCHING_TABLET1();
////         digitalWrite(relay1,LOW);
////          flag1=1;
//  }
// else if(customKey=='2')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("COLD SELECTED");
//          delay(1000);
//          DISPATCHING_TABLET2();
//
//  }
//  else if(customKey=='3')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("COUGH SELECTED");
//          delay(1000);
//          DISPATCHING_TABLET3();
//  }
//  else if(customKey=='4')
//  {
//          lcd.clear();
//          lcd.setCursor(0,0);
//          lcd.print("HEADACHE SELECTED");
//          delay(1000);
//          DISPATCHING_TABLET4();
//  }
//  else
//  {
//          lcd.clear();
//           lcd.setCursor(0,0);
//          lcd.print("NO TABLET ");
//          lcd.setCursor(0,1);
//          lcd.print("AVAILABLE TRY LATER ");
//          delay(3000);
//          lcd.clear();
//  }
//}
void clearData()
{
  while(data_count !=0)
  {
    Data[data_count--] = 0; 
  }
  return;
}


void RFID_CHECKING()
{
   lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("SCAN YOUR HEALTH  ");
    lcd.setCursor(0,1);
    lcd.print("CARD......   ");
 while(1)
 {
    if(Serial2.available()>0)
    {
     
      message=Serial2.readString();
      int str_len = message.length() + 1;
      char textmessage[12];
      message.toCharArray(textmessage,str_len);
      Serial.println(textmessage);
      textmessage[12]='\0';
      if((strcmp(textmessage,"3A001AB9C158"))==0)
      { 
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("PATIENT:PRASHANTH");
          lcd.setCursor(0,1);
          lcd.print("DOCTOR NAME:NAGESH");
          delay(4000);
          PASSWARD();
      
         // break;
      }
        if(!strcmp(textmessage,CARD_2))
      { 
           lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("NAME:SOWMYA");
          lcd.setCursor(0,1);
          lcd.print("DOCTOR NAME:SNEHA");
          delay(4000);
          PASSWARD();
   
          //break;
      }
        if(!strcmp(textmessage,CARD_3))
      { 
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("NAME:SOWMYA");
          lcd.setCursor(0,1);
          lcd.print("DOCTOR NAME:SNEHA");
          delay(4000);
          PASSWARD();
          
          //break;
      }
    }
 }
}


void PASSWARD()
{

   clearData();
   lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("PLEASE ENTER YOUR");
   lcd.setCursor(0,1);
  lcd.print("PASSWORD....");
  //int k=0;
  lcd.setCursor(0,2);
  while(1)
  {
   customKey = customKeypad.getKey();
   if (customKey)
    {
      Data[data_count] = customKey; 
    //  lcd.setCursor(0+i,1);
      //lcd.print(Data[data_count]);
       //Serial.println(data_count);
         Serial.print(Data[data_count]);
         //lcd.setCursor(0+i, 2);
    lcd.print("*");
      data_count++; 
   //delay(1000);
    }

  if(data_count == Password_Length-1){
  Serial.print("\r\n");
  
    if(!strcmp(Data,"1234"))
    {
//      lcd.print("Correct");
      Serial.println("Correct");
               lcd.clear();
                lcd.setCursor(0,0);
                lcd.print("NAME OF PATIENT:PRASHANTH");
                lcd.setCursor(0,1);
                lcd.print("PRESCRIBED MEDICNES ARE");
                lcd.setCursor(0,2);
                lcd.print("1.SUGAR    2.BP");
                 lcd.setCursor(0,3);
                lcd.print("3.CANCER   4.ALSUR");
                delay(3000);
                SELECT_MEDICINE();
          
                lcd.clear();
                break;
      }
//     else if(!strcmp(Data, "0007")){
////      lcd.print("Correct");
//      Serial.println("Correct");
//               lcd.clear();
//                lcd.setCursor(0,0);
//                lcd.print("NAME OF AGENT:ANIL");
//                lcd.setCursor(0,1);
//                lcd.print("MOBILE NO:7411417000");
//                lcd.setCursor(0,2);
//                lcd.print("EMPLOY ID:0007");
//                delay(8000);
//                flag=1;
//                lcd.clear();
//                break;
//      }
//       else if(!strcmp(Data, "1111"))
//       {
////      lcd.print("Correct");
//      Serial.println("Correct");
//               lcd.clear();
//                lcd.setCursor(0,0);
//                lcd.print("NAME OF AGENT:ARUN");
//                lcd.setCursor(0,1);
//                lcd.print("MOBILE NO:8411417000");
//                lcd.setCursor(0,2);
//                lcd.print("EMPLOY ID:1111");
//                delay(8000);
//                flag=1;
//                lcd.clear();
//                break;
//      }
    else
    {
     
       Serial.println("Incorrect");
       lcd.clear();
       lcd.print("Incorrect");
       lcd.setCursor(0,1);
       lcd.print("Please Try Again..");
      delay(1000);
      PASSWARD();
     
      }
    clearData();  
}
  }
}
void SELECT_MEDICINE()
{
    customKey=0;
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("PLEASE CHOOSE THE ");
    lcd.setCursor(0,1);
    lcd.print("MEDICINE FROM LIST");
    lcd.setCursor(0,2);
    lcd.print("1.SUGAR    2.BP");
    lcd.setCursor(0,3);
    lcd.print("3.CANCER    4.ALSUR");
    delay(2000);
  while(customKey==0) 
  customKey = customKeypad.getKey();
  if(customKey=='1')
  {
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("SUGAR TABLET SELECTED");
        //  DISPATCHING_TABLET1();
//          digitalWrite(relay1,HIGH);
         delay(1000);
          DISPATCHING_TABLET1();
//         digitalWrite(relay1,LOW);
//          flag1=1;
  }
 else if(customKey=='2')
  {
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("BP SELECTED");
          delay(1000);
          DISPATCHING_TABLET2();

  }
  else if(customKey=='3')
  {
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("CANCER SELECTED");
          delay(1000);
          DISPATCHING_TABLET3();
  }
  else if(customKey=='4')
  {
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("ALSUR SELECTED");
          delay(1000);
          DISPATCHING_TABLET4();
  }
  else
  {
          lcd.clear();
           lcd.setCursor(0,0);
          lcd.print("NO TABLET ");
          lcd.setCursor(0,1);
          lcd.print("AVAILABLE TRY LATER ");
          delay(3000);
          lcd.clear();
  }
}
 
void DISPATCHING_TABLET1(void)
{    
  
     lcd.clear();
     lcd.print("ENTER QUANTIY:");
     lcd.setCursor(0,1);

       l=1;
       m=0;
       while(l)
       {
          customKey = customKeypad.getKey();
          if (customKey)
          {
             delay(1000);
             if(customKey != '$')
            {
              lcd.print(customKey);
              
            }
            Tabbuff[m++]=customKey;
            if(customKey=='$')
            {   m--;
              Tabbuff[m]='\0';
              l=0;
            } 
          }
       }
          Tab_temp = atoi((char *)Tabbuff);
       if(Tab_temp < total_count_tab1)
      {
                    amount_temp = Tab_temp*10;
       if(amount_temp < total_amount)
      {
            lcd.clear();
           lcd.print("WAIT...               ");
           lcd.setCursor(0,1);
           lcd.print("DISPATCHING ");
           for(x=1;x<Tab_temp;x++)
           {
              l=1;
              z=0;
              lcd.clear();
               lcd.print("PLEASE COLLECT  ");
               lcd.setCursor(0,1);
               lcd.print(" YOUR TABLET  ");
             
               digitalWrite(relay1,HIGH);
               delay(1000);
               digitalWrite(relay1,LOW);
             delay(1000);
           }
             digitalWrite(voice,LOW);
               delay(1000);
               digitalWrite(voice,HIGH);
               delay(1000);
          Tab_temp = atoi(Tabbuff);
      
          total_count_tab1 = total_count_tab1 - Tab_temp;
            sprintf(buf11, "%d", total_count_tab1);
          lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REMAINING COUNT:");
               lcd.print(buf11);
//               lcd.setCursor(0,3);  
//           lcd.print(" THANK YOU        ");
          
        String  one = "REMAINING COUNT:";
        String  two = buf11; 
        String  three = "  THANK YOU";
        String  message = one +two + three ;
        int  str_len = message.length() + 1;
        char textmessage[str_len];
          message.toCharArray(textmessage,str_len);
          Serial.println(textmessage);        
      

      String  one1 =   "$REDUCED AMOUNT: ";
      //  String  two = amountbuf; 
       String   three1 = "#";
        String  message1 = one1 + amount_temp + three1 ;
        int str_len1 = message1.length() + 1;
        char  textmessage1[str_len];
          message1.toCharArray(textmessage1,str_len1);
          Serial.println(textmessage1);   
          
//          amount_temp = atoi(amountbuf);
      
          total_amount = total_amount - amount_temp;


       String  one2 =   "$BALANCE:";
      //  String  two = amountbuf; 
       String   three2 = "#";
        String  message2 = one2 + total_amount + three2 ;
        int str_len2 = message2.length() + 1;
        char  textmessage2[str_len2];
          message2.toCharArray(textmessage2,str_len2);
          Serial.println(textmessage2);        
            lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REDUCED AMOUNT:");
               lcd.print(amount_temp);
               lcd.setCursor(0,1);  
           lcd.print("REMAINING AMOUNT:");
             lcd.print(total_amount);
              lcd.setCursor(0,3);  
           lcd.print("THANK YOU");
   delay(2000);
      }
           else
           {
              lcd.clear();
              lcd.setCursor(0,0);
              lcd.print("INSUFFICIENT BALANCE");
              lcd.setCursor(0,1);
              lcd.print("RECHARGE UR CARD");
              delay(1000);
              Serial.print("$INSUFFICIENT BALANCE RECHARGE UR CARD#");
              delay(1000); 
           }
          l=0;
          loop();
      
        } 
      else
      {
      
             Serial.println("$SORRY, TABLET IS NOT IN STOCK#");
                lcd.clear();
                lcd.print("SORRY TABLET IS");
                lcd.setCursor(0,1);
                lcd.print("NOT IN STOCK"); 
                delay(3000);
                loop();
      } 
}
void DISPATCHING_TABLET2(void)
{    
  
     lcd.clear();
     lcd.print("ENTER QUANTITY:");
     lcd.setCursor(0,1);

       l=1;
       m=0;
       while(l)
       {
          customKey = customKeypad.getKey();
          if (customKey)
          {
             delay(1000);
             if(customKey != '$')
            {
              lcd.print(customKey);
              
            }
            Tabbuff[m++]=customKey;
            if(customKey=='$')
            {   m--;
              Tabbuff[m]='\0';
              l=0;
            } 
          }
       }
          Tab_temp = atoi((char *)Tabbuff);
       if(Tab_temp < total_count_tab2)
      {
       
            amount_temp = Tab_temp*20;
          if(amount_temp < total_amount)
      {
   
         lcd.clear();
           lcd.print("WAIT...               ");
           lcd.setCursor(0,1);
           lcd.print("DISPATCHING ");

           for(x=1;x<Tab_temp;x++)
           {
              l=1;
              z=0;
              lcd.clear();
               lcd.print("PLEASE COLLECT  ");
               lcd.setCursor(0,1);
               lcd.print(" YOUR TABLET  ");
             
               digitalWrite(relay2,HIGH);
               delay(1000);
               digitalWrite(relay2,LOW);
             delay(1000);
           }
             digitalWrite(voice,LOW);
               delay(1000);
               digitalWrite(voice,HIGH);
               delay(1000);
          Tab_temp = atoi(Tabbuff);
      
          total_count_tab2 = total_count_tab2 - Tab_temp;
          sprintf(buf22, "%d", total_count_tab2); 
                 lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REMAINING COUNT:");
               lcd.print(buf22);
//               lcd.setCursor(0,3);  
//           lcd.print(" THANK YOU        ");
          

        String  one = "REMAINING COUNT:";
        String  two = buf22; 
        String  three = "  THANK YOU";
        String  message = one +two + three ;
        int  str_len = message.length() + 1;
        char textmessage[str_len];
          message.toCharArray(textmessage,str_len);
          Serial.println(textmessage);        
//          SendSms(PHONE_NUMBER,textmessage);
           delay(1000);
           

      String  one1 =   "$REDUCED AMOUNT: ";
      //  String  two = amountbuf; 
       String   three1 = "#";
        String  message1 = one1 + amount_temp + three1 ;
        int str_len1 = message1.length() + 1;
        char  textmessage1[str_len];
          message1.toCharArray(textmessage1,str_len1);
          Serial.println(textmessage1);   
          
//          amount_temp = atoi(amountbuf);
      
          total_amount = total_amount - amount_temp;


       String  one2 =   "$BALANCE:";
      //  String  two = amountbuf; 
       String   three2 = "#";
        String  message2 = one2 + total_amount + three2 ;
        int str_len2 = message2.length() + 1;
        char  textmessage2[str_len2];
          message2.toCharArray(textmessage2,str_len2);
          Serial.println(textmessage2);        
          //sendSMS(PHONE_NUMBER_to_send,textmessage);
lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REDUCED AMOUNT:");
               lcd.print(amount_temp);
               lcd.setCursor(0,1);  
           lcd.print("REMAINING AMOUNT:");
             lcd.print(total_amount);
              lcd.setCursor(0,3);  
           lcd.print("THANK YOU");
   delay(2000);
      }
          else
           {
              lcd.clear();
              lcd.setCursor(0,0);
              lcd.print("INSUFFICIENT BALANCE");
              lcd.setCursor(0,1);
              lcd.print("RECHARGE UR CARD");
              delay(1000);
              Serial.print("$INSUFFICIENT BALANCE RECHARGE UR CARD#");
              delay(1000); 
           }
          l=0;
          loop();
      
        } 
      else
      {
      
 Serial.println("$SORRY, TABLET IS NOT IN STOCK#");
                lcd.clear();
                lcd.print("SORRY TABLET IS");
                lcd.setCursor(0,1);
                lcd.print("NOT IN STOCK"); 
                delay(1000);
                loop();
      } 
}
void DISPATCHING_TABLET3(void)
{    
  
     lcd.clear();
     lcd.print("ENTER QUANTIY:");
     lcd.setCursor(0,1);

       l=1;
       m=0;
       while(l)
       {
          customKey = customKeypad.getKey();
          if (customKey)
          {
             delay(1000);
             if(customKey != '$')
            {
              lcd.print(customKey);
              
            }
            Tabbuff[m++]=customKey;
            if(customKey=='$')
            {   m--;
              Tabbuff[m]='\0';
              l=0;
            } 
          }
       }
          Tab_temp = atoi((char *)Tabbuff);
       if(Tab_temp < total_count_tab3)
      {
        amount_temp = Tab_temp*30;
       if(amount_temp < total_amount)
      {
         lcd.clear();
           lcd.print("WAIT...               ");
           lcd.setCursor(0,1);
           lcd.print("DISPATCHING ");

           for(x=1;x<Tab_temp;x++)
           {
              l=1;
              z=0;
              lcd.clear();
               lcd.print("PLEASE COLLECT  ");
               lcd.setCursor(0,1);
               lcd.print(" YOUR TABLET  ");
              
               digitalWrite(relay3,HIGH);
               delay(1000);
               digitalWrite(relay3,LOW);
             delay(1000);
           }
            digitalWrite(voice,LOW);
               delay(1000);
               digitalWrite(voice,HIGH);
               delay(1000);
         
          
          Tab_temp = atoi(Tabbuff);
      
          total_count_tab3 = total_count_tab3 - Tab_temp;
          sprintf(buf33, "%d", total_count_tab3);
             lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REMAINING COUNT:");
               lcd.print(buf33);
//               lcd.setCursor(0,3);  
//           lcd.print(" THANK YOU        ");
        String  one = "REMAINING COUNT:";
        String  two = buf33; 
        String  three = "  THANK YOU";
        String  message = one +two + three ;
        int  str_len = message.length() + 1;
        char textmessage[str_len];
          message.toCharArray(textmessage,str_len);
          Serial.println(textmessage);        
//          SendSms(PHONE_NUMBER,textmessage);

      String  one1 =   "$REDUCED AMOUNT: ";
      //  String  two = amountbuf; 
       String   three1 = "#";
        String  message1 = one1 + amount_temp + three1 ;
        int str_len1 = message1.length() + 1;
        char  textmessage1[str_len];
          message1.toCharArray(textmessage1,str_len1);
          Serial.println(textmessage1);   
          
//          amount_temp = atoi(amountbuf);
      
          total_amount = total_amount - amount_temp;


       String  one2 =   "$BALANCE:";
      //  String  two = amountbuf; 
       String   three2 = "#";
        String  message2 = one2 + total_amount + three2 ;
        int str_len2 = message2.length() + 1;
        char  textmessage2[str_len2];
          message2. toCharArray(textmessage2,str_len2);
          Serial.println(textmessage2);        
          //sendSMS(PHONE_NUMBER_to_send,textmessage);
lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REDUCED AMOUNT:");
               lcd.print(amount_temp);
               lcd.setCursor(0,1);  
           lcd.print("REMAINING AMOUNT:");
             lcd.print(total_amount);
//              lcd.setCursor(0,3);  
//           lcd.print("THANK YOU");
lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REDUCED AMOUNT:");
               lcd.print(amount_temp);
               lcd.setCursor(0,1);  
           lcd.print("REMAINING AMOUNT:");
             lcd.print(total_amount);
              lcd.setCursor(0,3);  
           lcd.print("THANK YOU");
          delay(2000);
      }
           else
           {
              lcd.clear();
              lcd.setCursor(0,0);
              lcd.print("INSUFFICIENT BALANCE");
              lcd.setCursor(0,1);
              lcd.print("RECHARGE UR CARD");
              delay(1000);
              Serial.print("$INSUFFICIENT BALANCE RECHARGE UR CARD#");
              delay(1000); 
           }
          l=0;
          loop();
      
        } 
      else
      {
 Serial.println("$SORRY, TABLET IS NOT IN STOCK#");
                lcd.clear();
                lcd.print("SORRY TABLET IS");
                lcd.setCursor(0,1);
                lcd.print("NOT IN STOCK"); 
                delay(10000);
                loop();
      } 
}
void DISPATCHING_TABLET4(void)
{    
  
     lcd.clear();
     lcd.print("ENTER QUANTITY:");
     lcd.setCursor(0,1);

       l=1;
       m=0;
       while(l)
       {
          customKey = customKeypad.getKey();
          if (customKey)
          {
             delay(1000);
             if(customKey != '$')
            {
              lcd.print(customKey);
              
            }
            Tabbuff[m++]=customKey;
            if(customKey=='$')
            {   m--;
              Tabbuff[m]='\0';
              l=0;
            } 
          }
       }
          Tab_temp = atoi((char *)Tabbuff);
       if(Tab_temp < total_count_tab4)
      {
             amount_temp = Tab_temp*40;
       if(amount_temp < total_amount)
      {
         lcd.clear();
           lcd.print("WAIT...               ");
           lcd.setCursor(0,1);
           lcd.print("DISPATCHING ");
             for(x=1;x<Tab_temp;x++)
           {
              l=1;
              z=0;
              lcd.clear();
               lcd.print("PLEASE COLLECT  ");
               lcd.setCursor(0,1);
               lcd.print(" YOUR TABLET  ");
             
               digitalWrite(relay4,HIGH);
               delay(1000);
               digitalWrite(relay4,LOW);
             delay(1000);
           }
             digitalWrite(voice,LOW);
               delay(1000);
               digitalWrite(voice,HIGH);
               delay(1000);
          
          Tab_temp = atoi(Tabbuff);
      
          total_count_tab4 = total_count_tab4 - Tab_temp;
           sprintf(buf44, "%d", total_count_tab4);
              lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REMAINING COUNT:");
               lcd.print(buf44);
//               lcd.setCursor(0,3);  
//           lcd.print(" THANK YOU        ");
        String  one = "REMAINING COUNT:";
        String  two = buf44; 
        String  three = "  THANK YOU";
        String  message = one +two + three ;
        int  str_len = message.length() + 1;
        char textmessage[str_len];
          message.toCharArray(textmessage,str_len);
          Serial.println(textmessage);        
//          SendSms(PHONE_NUMBER,textmessage);
           delay(1000);
       
      String  one1 =   "$REDUCED AMOUNT: ";
      //  String  two = amountbuf; 
       String   three1 = "#";
        String  message1 = one1 + amount_temp + three1 ;
        int str_len1 = message1.length() + 1;
        char  textmessage1[str_len];
          message1.toCharArray(textmessage1,str_len1);
          Serial.println(textmessage1);   
          
//          amount_temp = atoi(amountbuf);
      
          total_amount = total_amount - amount_temp;


       String  one2 =   "$BALANCE:";
      //  String  two = amountbuf; 
       String   three2 = "#";
        String  message2 = one2 + total_amount + three2 ;
        int str_len2 = message2.length() + 1;
        char  textmessage2[str_len2];
          message2.toCharArray(textmessage2,str_len2);
          Serial.println(textmessage2);        
          //sendSMS(PHONE_NUMBER_to_send,textmessage);
lcd.clear();
              lcd.setCursor(0,0);
               lcd.print("REDUCED AMOUNT:");
               lcd.print(amount_temp);
               lcd.setCursor(0,1);  
           lcd.print("REMAINING AMOUNT:");
             lcd.print(total_amount);
              lcd.setCursor(0,3);  
           lcd.print("THANK YOU");
            delay(2000);
      }
        else
           {
              lcd.clear();
              lcd.setCursor(0,0);
              lcd.print("INSUFFICIENT BALANCE");
              lcd.setCursor(0,1);
              lcd.print("RECHARGE UR CARD");
              delay(1000);
              Serial.print("$INSUFFICIENT BALANCE RECHARGE UR CARD#");
              delay(1000); 
           }
          l=0;
          loop();
      
        } 
      else
      {
 Serial.println("$SORRY, TABLET IS NOT IN STOCK#");
                lcd.clear();
                lcd.print("SORRY TABLET IS");
                lcd.setCursor(0,1);
                lcd.print("NOT IN STOCK"); 
                delay(1000);
                loop();
      } 
}
void buff_clear()
{
  for(int i=0;i<4;i++)
  buff[i]=0;
}
