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

void XOR()   // if 11 and 00 then 0(false)
{
    printf("%s  \n", crcResult);
    //printf("%s \n", crcKey);
    for (index_j = 1; index_j < CRC_Key_LENGTH; index_j++)
    {
        crcResult[index_j] = ((crcResult[index_j] == crcKey[index_j]) ? '0' : '1');
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
    if (errorDetected)
    {
        printf("\nError in transmission \n\n");
    }
    else
    {
        printf("\nNo Error in transmission \n\n");
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
    printf("\n Data padded with n-1 zeros: %s \n", inputData);
    performCRC();
    printf("\n CRC Key Append value is: %s \n", crcResult);
    printf("\nChoose an option:\n");
    printf("1. No error\n");
    printf("2. Introduce an error\n");
    int option;
    scanf("%d", &option);
    if (option == 1)
    {
        for (index_i = inputDataLength; index_i < inputDataLength + CRC_Key_LENGTH - 1; index_i++)
        {
            inputData[index_i] = crcResult[index_i - inputDataLength];
        }
    }
    else if (option == 2)
    {
        printf("\nEnter the index of the bit to flip (0 to %lu): ", inputDataLength + CRC_Key_LENGTH - 2);
        int flipIndex;
        scanf("%d", &flipIndex);
        if (flipIndex >= 0 && flipIndex <= inputDataLength + CRC_Key_LENGTH - 2)
        {
            inputData[flipIndex] = (inputData[flipIndex] == '0') ? '1' : '0';
            printf("\nBit at index %d flipped.", flipIndex);
        }
        else
        {
            printf("\nInvalid index. No bit flipped.\n");
        }
    }
    printf("\nData sent to the Sender: %s \n", inputData);
    receiverCheck();
    return 0;
}
# Hamming 
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <limits.h>
#include <math.h>

int bitString[17];
int h[4];
int ind = 0;

int calc(int *bitString, int n, int k)
{
    int cnt = 0;
    int p = 0;
    int i = k;

    while (i <= n)
    {
        if (cnt < k)
        {
            if (bitString[i] == 1)
                p++;
            cnt++;
            i++;
        }

        else
        {
            cnt = 0;
            i = i + k;
        }
    }

    if (p % 2 == 0)
    {
        bitString[k] = 0;
    }
    else
        bitString[k] = 1;

    if (p % 2 != 0)
        p++;

    if (p % 2 == 0)
    {
        h[ind++] = 0;
    }
    else
        h[ind++] = 1;
}

int calc1(int *bitString, int n, int k)
{
    int cnt = 0;
    int p = 0;
    int i = k;

    while (i <= n)
    {
        if (cnt < k)
        {
            if (bitString[i] == 1)
                p++;
            cnt++;
            i++;
        }
        else
        {
            cnt = 0;
            i = i + k;
        }
    }

    if (p % 2 == 0)
    {
        h[ind++] = 0;
    }
    else
        h[ind++] = 1;
}

int main()
{
    int n;
    int data;
    printf("Enter size : ");
    scanf(" %d", &n);
    printf("Enter Data : ");
    scanf(" %d", &data);
    bitString[0] = -2;
    bitString[1] = -1;
    bitString[2] = -1;

    for (int i = 1; i <= n; i++)
    {

        if (i != 1 && i != 2 && i != 4 && i != 8 && i != 16)
        {
            scanf(" %d", &data);
            bitString[i] = data;
        }
        else
        {
            n++;
            bitString[i] = -1;
        }
    }

    for (int j = 1; j <= n; j++)
    {
        printf("%d ", bitString[j]);
    }

    calc(bitString, n, 1);
    calc(bitString, n, 2);
    calc(bitString, n, 4);
    calc(bitString, n, 8);
    printf("\n\n");

    for (int j = 1; j <= n; j++)
    {
        printf("%d ", bitString[j]);
    }

    printf("\n\n");

    printf("p1 : %d ", bitString[1]);
    printf("p2 : %d ", bitString[2]);
    printf("p4 : %d ", bitString[4]);
    printf("p8 : %d ", bitString[8]);

    printf("\n\n");

 printf("Code is right ...\n");

    for (int i = 0; i < 4; i++)
    {
        printf("%d ", h[i]);
    }

    // erorr detection

    printf("\n\n");
    ind = 0;

    bitString[5] = 0;

    for (int j = 1; j <= n; j++)
    {
        printf("%d ", bitString[j]);
    }

    printf("\n\n");

    calc1(bitString, n, 1);
    calc1(bitString, n, 2);
    calc1(bitString, n, 4);
    calc1(bitString, n, 8);

    printf("\n\n");

    printf("Code is right ...\n");

    for (int i = 0; i < 4; i++)
    {
        printf("%d ", h[i]);
    }



return 0;
}

# DSSS
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char data[3];
char xnorValue[11];

char pattern[11] = {'1', '0', '1', '1', '0', '1', '1', '1', '0', '0', '0'};
char res[33];
int ptr = 0;

void xnorfunction(char bit, char pattern[]) {
    for (int k = 0; k < 11; k++) {
        xnorValue[k] = bit;
    }

    for (int j = 0; j < 11; j++) {
        res[ptr] = (xnorValue[j] == pattern[j] ? '1' : '0');
        ptr++;
    }
}

int main() {
    printf("Enter data to be sent: ");
    scanf("%s", data);

    for (int i = 0; i < 3; i++) {
        xnorfunction(data[i], pattern);
    }

    for (int l = 0; l < 33; l++) {
        printf("%c", res[l]);
    }
    return 0;
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



