**Body Temperature Monitor Alerts (BTM)**

` `**Contents**

- **Abstract**
- [**Introduction**](https://github.com/nazirakz/sciot-CGMalerts#introduction)
- [**Architecture**](https://github.com/nazirakz/sciot-CGMalerts#architecture)
- **How to [Installation and run](https://github.com/nazirakz/sciot-CGMalerts#installationrun) it**


**Abstract**

Abstract. In general, medical equipment at the Hospital to monitor body temperature such as a thermometer is less effective for nurses and doctors because it takes time to come to the patient's room to retrieve body temperature data. Then, when the patient's condition suddenly worsens, it still takes time for the treatment process. Therefore, by using sensor/simulator that can help nurses to monitor the patient's body temperature through an Android smartphone in real time and The Body Temperature Alert can send notifications in the form of Email when the patient's body temperature is outside the normal limit. The working principle of the body temperature data received by the BTM sensor. **BTM Alerts** is a project created to demonstrate Server less computing capabilities by using a simulated body temperature sensor and functions that trigger actions and generate alerts. This project contains a simulated BTM sensor that uses a random function to generate the temperature value and the system will send alerts to the doctor by using IFTTT Web hooks and email services.


**Introduction**

Vital signs of human health can be identified from body temperature, heart rate, respiratory, and blood pressure. From these vital signs, body temperature is a sign that needs most attention. Body temperature is the difference between the amount of heat produced by body processes and the amount of heat lost to the outside environment. Normal temperatures in adults range from 36.5 °C - 37.5 °C [2]. If the body temperature is below 36 °C, hypothermia is indicated, but if the body temperature is above 37 °C, it is indicated that one suffers from hyperthermia. Body temperature is an equally important parameter in checking body condition. Unhealthy physical

My main concern is when the patients are in critical situation like after surgery or before surgery, the doctor needs patient’s history and the quick action should be perform when the temperature goes up.

` `Therefore by using Serverless functions I have tried to solve these problems

1) Having history of body temperature data to understand the effectiveness of treatment.
1) To have a direct connection with the doctor in critical situations. 

**BTM Alerts** is a project created to demonstrate Serverless computing capabilities by using a simulated body temperature sensor and functions that trigger actions and generate alerts. This project contains a simulated BTM sensor that uses a random function to generate the temperature value. If values are more than 37 °C, it means that the health of the patient is in critical status, and the system will send alerts to the doctor by using IFTTT webhooks and email services. 




**Architecture**

To send the body Temperature values there is the function **'healthdata'** on Nuclio. This function generates random body temperature values simulating a real BTM sensor. These values are published on the queue **'iot/bodytemperature'** of RabbitMQ. When a message is published in the queue **'iot/ bodytemperature '**, the function **' bodyTemp '** is triggered. This function consumes the message on the queue and takes decisions: it sends the value of the body temperature to an app client through the MQTT protocol and if the value is above the given threshold is sent an email to the doctor through IFTTT triggers. It can also builds a log with the date and time of all entries published on the **'iot/ bodytemperature '** queue.

![C:\Users\CL\Desktop\archetecture BTM.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.001.png)

**How to [Installation and run](https://github.com/nazirakz/sciot-CGMalerts#installationrun) it**

- Install [Docker](https://docs.docker.com/engine/install/) from the official website
- Install RabbitMQ by running:

` `docker run -p 9000:15672  -p 1883:1883 -p 5672:5672  cyrilix/rabbitmq-mqtt

- Install Nuclio by running:

docker run -p 8070:8070 -v /var/run/docker.sock:/var/run/docker.sock -v /tmp:/tmp nuclio/dashboard:stable-amd64

- Create your IFTTT account, create a new Applet with event name mailtrigger, WebHooks in the 'if" section and 'Email' in the then section.

![](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.002.png)

**Install required libraries**

npm install mqtt

npm install amqplib

npm install request



- Deploy the healthdata.js function on Nuclio, it will create the queue iot/bodytemperature on RabbitMQ and send a random value accordingly.

![C:\Users\CL\Desktop\nuclio deployed.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.003.png)





- Run the BodyTemp.js function, it will start to show all the values published on the queue and their timestamp. 

![C:\Users\CL\Desktop\mqtt.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.004.png)

![C:\Users\CL\Desktop\mqttt.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.005.png)

![C:\Users\CL\Desktop\mqt.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.006.png)

![C:\Users\CL\Desktop\values.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.007.png)

- Run the bodytemp.js function to start to read values from the queue iot/ bodytemperature and basing on the value found, it will take some actions. If the value is in the safe health range (33-37) it will send the value to an app client using mqtt, while if the value is above the safe health range (38) it will also send an email to the doctor to warn about the patient's health. 



![C:\Users\CL\Desktop\ifttt.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.008.png)

- I have logged into IFTTT by using by email and made my Applet by providing the required information.

![C:\Users\CL\Desktop\ifttt email.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.009.png)

- I Got Email If the body temperature is high.

![C:\Users\CL\Desktop\ifttt edit.PNG](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.010.png)

- If you want to edit your Applet then you can edit from settings




![C:\Users\CL\Desktop\1.jpg](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.011.jpeg)     ![C:\Users\CL\Desktop\21.jpg](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.012.jpeg)    ![C:\Users\CL\Desktop\2.jpg](Aspose.Words.080e92a9-d752-4f94-935d-0076552f9ee2.013.jpeg)



- **Android IOT Client**: In this App I connected to the RabbitMQ broker using the protocol MQTT. After creating the connection, I can send or receive values on iot/bodytemperature.










**Note:** in the above mentioned functions you need to change the IP address with your own IP address and the API KEY of the IFTTT function with the APY KEY you will find in Settings after you create your own function.

