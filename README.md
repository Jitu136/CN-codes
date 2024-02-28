# CN-codes
CN pratice codes
# CRC 
#include <stdio.h>
#include <string.h>

#define CRC_Key_LENGTH strlen(crcKey)

char inputData[28];
char crcResult[28];
char crcKey[10];
int inputDataLength, index_i, index_j;

void performCRC();

void XOR()
{
    printf("%s  ", crcResult);
    printf("%s \n", crcKey);
    for (index_j = 1; index_j < CRC_Key_LENGTH; index_j++)
    {
        crcResult[index_j] = ((crcResult[index_j] == crcKey[index_j]) ? '0' : '1');
    }
}

void error()
{
    printf("\n If You want to put error enter 1 otherwise 0 : ");
    int error = 0;
    scanf("%d", &error);
    if (error == 1)
    {
        printf("\n How many bits you have to change : ");
        int n = 0;
        scanf("%d", &n);
        for (int i = 0; i < n; i++)
        {
            printf("\n Enter the bit index which you want to change:");
            int num;
            scanf("%d", &num);

            // Add a space before %c to consume the newline character
            char bit;
            printf("Enter 1 or 0 : ");
            scanf(" %c", &bit);

            inputData[num] = bit;
        }
    }
}

void receiverCheck()
{

    printf("\nData received: %s \n", inputData);

    performCRC();

    int errorDetected = 0;

    for (index_i = 0; index_i < CRC_Key_LENGTH - 1; index_i++)
    {
        if (crcResult[index_i] == '1')
        {
            errorDetected = 1;
            break;
        }
    }

    if (!errorDetected)
    {
        printf("\n Data accepted  \n\n");
    }
    else
    {
        printf("\n Data rejected  \n\n");
    }
}

void performCRC()
{

    for (index_i = 0; index_i < CRC_Key_LENGTH; index_i++)
    {
        crcResult[index_i] = inputData[index_i];
    }

    do
    {
        if (crcResult[0] == '1')
        {
            XOR();
        }

        for (index_j = 0; index_j < CRC_Key_LENGTH - 1; index_j++)
        {
            crcResult[index_j] = crcResult[index_j + 1];
        }

        crcResult[index_j] = inputData[index_i++];

    } while (index_i <= inputDataLength + CRC_Key_LENGTH - 1);
    printf("%s  ", crcResult);
}

int main()
{
    printf("\nEnter data to be transmitted: ");
    scanf("%s", inputData);
    printf("\nEnter the CRC-Key: ");
    scanf("%s", crcKey);

    inputDataLength = strlen(inputData);

    for (index_i = inputDataLength; index_i < inputDataLength + CRC_Key_LENGTH - 1; index_i++)
    {
        inputData[index_i] = '0';
    }

    printf("\n *****  Data padded with n-1 zeros: %s \n", inputData);

    performCRC();

    printf("\n *****   CRC Key Append value is: %s \n", crcResult);

    for (index_i = inputDataLength; index_i < inputDataLength + CRC_Key_LENGTH - 1; index_i++)
    {
        inputData[index_i] = crcResult[index_i - inputDataLength];
    }

    printf("\nData sent to the Sender: %s \n", inputData);

    error();

    receiverCheck();

    return 0;
}
# Hamming 
#include <stdio.h> 
#include <string.h> 
#include <math.h> 
#define N 50  
char *fill_parity(char data1[],int p)
{     char q[N];     
while(p>=1){         
     int count = 0, a=0;        
     int i = pow(2,p-1);         
     int z = i;         
     for(i=z-1; i<strlen(data1); i++)
     {if(count == z)
     {i=i+z-1;                 
     count = 0;             }
     else{q[a] = data1[i]; a++;
     count++;
}
}
q[a] = '\0';
printf("For Parity at %dth: %s\n", z, q);
count = 0;
for (i = 0; i < strlen(q); i++)
{
    if (q[i] == '1')
    {
        count++;
    }
}
if (count % 2 == 0)
{
    data1[z - 1] = '0';
}
else
{
    data1[z - 1] = '1';
}
printf("Data: %s\n", data1);
p--;
}
return data1;
}
char *error(char data[])
{
    printf("------------------------------------------------------------\n");
    printf("Final data: ");
    for (int d = 0; d < strlen(data); d++)
    {
        printf("%c", data[d]);
    }
    printf("\n");
    printf("Enter position for switching a bit:");
    int pos;
    scanf("%d", &pos);
    if (data[pos - 1] == '0')
    {
        data[pos - 1] = '1';
    }
    else
    {
        data[pos - 1] = '0';
    }
    printf("Data after fliping a bit: %s\n", data);
    printf("------------------------------------------------------------\n");
    return data;
}
void receive(char data[]);
void reverseString(char *str);
int main()
{
    char data[N];
    int choice;
    do
    {
        printf("Enter Data: ");
        scanf("%s", data);
        data[strlen(data)] = '\0';
        reverseString(data);
        int data_sz = strlen(data);
        int p = 1;
        while (1)
        {
            if (pow(2, p) >= data_sz + p + 1)
            {
                break;
            }
            p++;
        }
        printf("Required number of Parity Bits: %d\n", p);
        int j = 0, k = 0, l = 0;
        char data1[N];
        for (j = 0; j < data_sz + p; j++)
        {
            if (j + 1 == pow(2, k))
            {
                data1[j] = 'p';
                k++;
            }
            else
            {
                data1[j] = data[l];
                l++;
            }
        }
        data1[data_sz + p] = '\0';
        printf("Data with parity bits: %s\n", data1);
        fill_parity(data1, p);
        printf("1.Enter [a] to pass data without error.\n2.Enter [b] to pass data with error.\n");
        char ch;
        scanf(" %c", &ch);
        switch (ch)
        {
        case 'a':
            receive(data1);
            break;
        case 'b':
            error(data1);
            receive(data1);
            break;
        default:
            printf("Wrong Input.");
        }
        printf("\nDo you want to continue: [yes(1)/no(0)] ->");
        scanf("%d", &choice);
        if (choice == 0)
        {
            printf("\n-----------------------Thank You!---------------------------\n\n");
        }
        else
        {
            printf("------------------------------------------------------------\n\n");
        }
    } while (choice == 1);
    return 0;
}
void receive(char data[])
{
    int i = 0;
    char q[N];
    int arr[N];
    while (1)
    {
        if (strlen(data) <= pow(2, i))
        {
            break;
        }
        i++;
    }
    int y = i;
    while (i != 0)
    {
        int a = 0, count = 0;
        int s = pow(2, i - 1);
        for (int j = s - 1; j < strlen(data); j++)
        {
            if (count == s)
            {
                j = j + s - 1;
                count = 0;
            }
            else
            {
                q[a] = data[j];
                a++;
                count++;
            }
        }
        q[a] = '\0';
        printf("For Parity at %dth: %s\n", s, q);
        count = 0;
        for (int k = 1; k < strlen(q); k++)
        {
            if (q[k] == '1')
            {
                count++;
            }
        }
        if (count % 2 == 0 && q[0] == '0')
        {
            arr[i - 1] = 0;
        }
        else if (count % 2 != 0 && q[0] == '1')
        {
            arr[i - 1] = 0;
        }
        else
        {
            arr[i - 1] = 1;
        }
        i--;
    }
    int flag = 1;
    printf("Resultant Array:");
    for (int e = y - 1; e >= 0; e--)
    {
        printf("%d", arr[e]);
        if (arr[e] == 1)
        {
            flag = 0;
        }
    }
    printf("\n------------------------------------------------------------\n");
    if (flag == 0)
    {
        printf("!!! Data Rejected !!!\n");
    }
    else
    {
        printf("!!! Data Accepted !!!\n");
    }
    printf("------------------------------------------------------------\n");
}
void reverseString(char *str)
{
    int length = strlen(str);
    int start = 0;
    int end = length - 1;
    while (start < end)
    {
        char temp = str[start];
        str[start] = str[end];
        str[end] = temp;
        start++;
        end--;
    }
}
# DSSS
#include <stdio.h>
#include <string.h>
#define S 110

char *org_signl(char data[], char data1[]);
char *XNOR(char data1[], char result[]);
char *org_dataa(char org_result[], char org_data[]);

void main(){
    char data[10];
    char data1[S];
    char result[S];
    char org_result[S];
    char org_data[10];
    printf("Enter Data: ");
    scanf("%s",data);
    org_signl(data,data1);
    printf("%s\n",data1);
    XNOR(data1,result);
    printf("Result: %s\n",result);
    XNOR(result,org_result);
    printf("Org_result: %s\n",org_result);
    org_dataa(org_result,org_data);
    printf("Org_Data: %s\n",org_data);

}
	
char *org_signl(char data[], char data1[]){
    int k=0;
    for(int i=0; i<strlen(data); i++){
        if(data[i]=='1'){
            for(int j=0; j<11; j++){
                data1[k++] = '1';
            }
        }else{
            for(int j=0; j<11; j++){
                data1[k++] = '0';
            }
        }
    }
    data1[strlen(data)*11]='\0';
    return data1;
}

char *XNOR(char data1[], char result[]){
    char pattern[] = {'1','0','1','1','0','1','1','1','0','0','0'};
    int l = strlen(data1)/11;
    int k=0;
    while(l>0){
        for(int i=0; i<11; i++){
            if(data1[k]==pattern[i]){
                result[k++]='1';
            }else{
                result[k++]='0';
            }
        }
        l--;
    }
    result[strlen(result)]='\0';
    return result;
}

char *org_dataa(char org_result[], char org_data[]){
    int flag=-1, k=0, j=0;
   
 int l = strlen(org_result)/11;
    int m=l;
    while(l>0){
        for(int i=0; i<11; i++){
            if(org_result[k]=='1'){
                flag=1;
            }else if(org_result[k]=='0'){
                flag=0;
            }else{
                flag=-1;
            }
            k++;
        }
        // printf("%d\n",flag);
        if(flag==1){
            org_data[j++]='1';
        }else if(flag==0){
            org_data[j++]='0';
        }else{
            printf("Something Wrong\n");
        }
        l--;
    }
    org_data[m]='\0';
    return org_data;
}

# FHSS
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define size 32

void sender(int *dataPackets, int packets){
    int *frequencies = (int *)malloc(sizeof(int) * 32);
    srand(time(NULL));
    for(int i=0; i<packets; i++){
        frequencies[i] = rand() % 100;
    }

    printf("Transmitting %d packets using Frequency hopping spread spectrum\n",packets);
    for(int i=0; i<packets; i++){
        printf("Data packet %d send with frequency: %d\n",i+1,frequencies[i]);
        printf("Data: %d\n",dataPackets[i]);
    }
}

void receiver(int *dataPackets, int packets){
    printf("Data received:\n");
    for(int i=0; i<packets; i++){
        printf("Data packet %d: %d\n",i+1,dataPackets[i]);
    }
}

int main()
{
    int packets;
    printf("Enter number of packets to send\n");
    scanf("%d", &packets);

    int *dataPackets = (int *)malloc(sizeof(int) * size);
    for (int i = 0; i < packets; i++)
    {
        printf("Enter data packet %d\n", i + 1);
        scanf("%d", &dataPackets[i]);
    }

    sender(dataPackets, packets);
    receiver(dataPackets, packets);

    free(dataPackets);
    return 0;
}

# FRAMING METHODS
#1. FLaG WITH BIt
  #include <stdio.h>
#include <string.h>

char flag[]={'0','1','1','1','1','1','1','0'};
#define s 50

void receiver(char data[]){
    char res[s];
    int z, j, k=0;
    int count=0;
    for(int i=0;i< strlen(data);i++){
        count=0;
        if(data[i]=='0'){
            count++;
            for( j=i+1;j<i+8;j++){
                if(data[j]=='1'){
                    count++;
                }else{
                    break;
                }
            }
        }
        if(count==7){
            i=j;
        }else{
            res[k++]=data[i];
        }
    }
    res[strlen(data)-16]='\0';
    printf("Data after Flag bytes dropped by reciever: %s\n",res);
    k=0;
    int count1=0;
    for(int i=0; i<strlen(res); i++){
        count=0;

        if(res[i]=='0'){
            data[k++]= res[i];
            for(int j=i+1;j<i+7;j++){
                if(res[j]=='1'){
                    data[k++]=res[j];
                    count++;
                }
                else{
                    break;
                }
            }
        }

        if(count==5){
            i=j;
            count1++;
        }
        else{
            data[k++]=res[i];
        }
    }
    data[strlen(res)-count1]='\0';
    printf("Orignal Data recieved: %s\n",data);
}

int main() {
    char data[s];
    char data1[s];
    printf("Enter the data: ");
    scanf("%s",&data);
    data[strlen(data)]='\0';

    //after zero --continous five 1's stuff zero.
    int count=0;
    int count1=0;
    int k=0;
    for(int i=0;i< strlen(data);i++){

  data1[k++]=data[i];
        if(data[i]=='0'){
            count=0;
            for(int j=i+1;j< i+6;j++){
                data1[k++]=data[j];
                if(data[j]=='1'){
                    count++;
                    if(count==5){
                        count1++;
                        data1[k++]='0';
                    }
                }
                else{
                    i=j;
                    break;
                }
                i=j;
            }

        }
    }
    data1[strlen(data)+count1]='\0';
    printf("Data after stuffing 0's: %s \n",data1);

    //flag.
    for( int i=0;i< strlen(data1)+16;i++) {
        if(i<8){
            data[i]=flag[i];
        }else if(i>strlen(data1)+7){
            data[i]=flag[i-strlen(data1)-8];
        }else{
            data[i]=data1[i-8];
        }
    }

data[strlen(data1)+16]='\0';
    printf("Final Data to be sent: %s\n",data);

  receiver(data);
}
#2 . BYTE COUNT
#include <stdio.h>
#include <string.h>
#define S 20

int data2[S];

void sender(){
    int data1[S];
    int data;
    int data_len=0, i=0;
    int last_digit;
    printf("Enter Data:");
    scanf("%d",&data);

    while(data!=0){
        last_digit=data%10;
        data=data/10;
        data1[i++]=last_digit;
        data_len++;
    }
    data2[0]=data_len+1;
    int k=1;
    for(int i=data_len-1; i>=0; i--){
        data2[k++]=data1[i];
    }

 printf("Enter sender's data:");
    for(int i=0; i<data_len+1; i++){
        printf("%d",data2[i]);
    }
    printf("\n");
}

void receiver(int data[]){
    char rev_data[S];
    int data_sz = data[0];
    int j=0;
    for (int i = 1; i < data_sz; i++)
    {
        rev_data[j++]=data[i];
    }
    printf("Original received data: ");
    for(int i=0; i<data_sz-1; i++){
        printf("%d",rev_data[i]);
    }
    printf("\n");
}
int main(){
    sender();
    receiver(data2);
}

#3. FLAG WITH BYTE stuffing
// flag bytes with byte stuffing Framing Method
#include <stdio.h>
#include <string.h>
#define S 100

char flag[]={'0','1','1','1','1','1','1','0'};
char esc[]={'0','0','0','1','1','0','1','1'};

void reciever(char data[]){
    char res[S];
    int count=0, count1=0, j=0, k=0, l=0, p=0;
    while(1){
        int isFlag=0, isEsc=0;
        for(int i=0; i<8; i++){
            if(data[j+i]==flag[i]){
                isFlag=1;
            }else{
                isFlag=0;
                break;
            }
        }
        for(int m=0; m<8; m++){
            if(data[j+m]==esc[m]){
                isEsc=1;
            }else{
                isEsc=0;
                break;
            }
        }
        if(isFlag==1 && count1==0){
            count++;
            l++;
        }else if(isEsc==1 && count1==0){
      
      count1++;
            l++;
        }else{
            for(int i=0; i<8; i++){
                res[p++]=data[j+i];
            }
            count1=0;
        }
        j+=8;
        if(count==2){
            break;
        }
    }
    res[strlen(data)-l*8]='\0';
    printf("Original Data is %s",res);
}

int main(){
    char data[S];
    char data1[S];
    char data2[S];

    printf("Enter Data:");
    scanf("%s",data);
    data[strlen(data)] = '\0';

    //covert bits to bytes by appending leading zero's
    int c_bytes;
    if(strlen(data)%8 == 0){
        c_bytes = strlen(data)/8;
        for(int i=0; i<c_bytes*8; i++){
            data1[i]=data[i];
        }
        data1[c_bytes*8]='\0';
    }else{
        c_bytes = (strlen(data)/8)+1;
        for(int i=0; i<(c_bytes*8)-strlen(data); i++){
           
 data1[i] = '0';
        }
        int j=0;
        for(int i=(c_bytes*8)-strlen(data); i<c_bytes*8; i++){
            data1[i] = data[j++]; 
        }
        data1[c_bytes*8]='\0';
    }
    printf("Data is of %d bytes.\n",c_bytes);
    printf("Data Converted in Bytes format: %s\n",data1);

    //append esc if data bits are equal to flag or esc key
    int j=0, k=0, count1=0; 
    int count = c_bytes;
    while(1){
        int isFlag=1, isEsc=1;
        for(int i=0; i<8; i++){
            if(data1[j+i]!=flag[i]){
                isFlag=0;
                break;
            }
        }
        
        for(int m=0; m<8; m++){
            if(data1[j+m]!=esc[m]){
                isEsc=0;
                break;
            }
        }
        if(isFlag==1 || isEsc==1){
            count1++;
            for(int i=0; i<8; i++){
                data2[k++] = esc[i];
            }
        }
        for(int l=0; l<8; l++){
            data2[k++]=data1[j++];
    
    }
        count--;
        if(count==0){
            break;
        }
        }
        data2[strlen(data1)+count1*8]='\0';
        printf("Data after inserting Esc key if data having flag or Esc byte: %s\n",data2);

        //create final data by appending flag at both ends
        for(int i=0; i<8; i++){
            data1[i] = flag[i];
        }
        for(int i=0; i<strlen(data2); i++){
            data1[8+i] = data2[i];
        }
         for(int i=0; i<8; i++){
            data1[8+strlen(data2)+i] = flag[i];
        }
        data1[16+strlen(data2)]='\0';
        printf("Final data to be sent: %s\n",data1);

        reciever(data1);
        return 0;
    }

    #STOP AND WAIT
    #include <stdio.h>
#include <stdlib.h>

int numofPacket;
float tp;
float tt;
float time = 0;
int i, j, k;

void sender();
void receiver();

void sender(){
    printf("\nEnter number of packets :");
    scanf("%d", &numofPacket);
    
    int packet[numofPacket];

    printf("\nEnter packets : ");
    for(i = 0; i < numofPacket; i++){
        scanf("\n%d", &packet[i]);
    }

    printf("\nEnter transmission time in ms : ");
    scanf("%f", &tt);

    printf("\nEnter propagation time in ms : ");
    scanf("%f", &tp);

    printf("\nTheoretically time taken to transmit %d number of packets should be %f", numofPacket, numofPacket*2*(tt+tp));
    printf("\n");
    
    for(i = 0; i < numofPacket; i++){
        time += tt;
        printf("\nTime taken to transmit '%d' at %f is ", packet[i], time);
        receiver();
    }
    printf("\n");
    printf("\nTotal time taken is %f", time);
    printf("\n");
}

void receiver(){
    time += tp;
    printf("\nPacket received at %f", time);
    time += tt;
    printf("\nAcknowledgement sent at %f", time);
    time += tp;
    printf("\n");
}

int main(){
    sender();
    return 0;
}



