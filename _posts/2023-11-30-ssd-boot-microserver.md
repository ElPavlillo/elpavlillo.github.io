---
layout: post
title:  Boot desde el ssd instalado en la baia ODD del HP Microserver Gen8
date: 2023-11-30 15:01:00
description: Como hacer boot desde ssd en un HP Microserver Gen8
tags: microserver ssd boot
categories: servers
thumbnail: assets/img/boot_ssd/microserver.png
featured: false
---

El HP Microserver Gen8 es un servidor muy competente por el precio que tiene actualmente (en concreto si le cambias la CPU que tiene de stock y le pones la máxima cantidad de RAM), tanto si lo quieres para montar un NAS como un servidor de virtualización, ahora, dicho esto, tiene un fallo de diseño que os puede volver un poco locos como a mi.

**NO HACE BOOT DESDE EL PUERTO SATA EXTRA NATIVAMENTE**

Pero este gran fallo tiene un workaround relativamente sencillo. Usando el controlador Smart Array B120i RAID integrado podemos crear una unidad lógica y luego seleccionarla como unidad de arranque, como vamos a ver a continuación:

> **ADVERTENCIA**: Muchos OS actuales destinados a virtualización o almacenamiento en red no recomiendan el uso de controladores de RAID por hardware o controladores SAS, porque requieren acceso directo al disco para el correcto funcionamiento del RAID por software, pero como este
apaño solo es para la unidad de arranque a mi no me ha dado ningún problema.

##### **Paso 1:**
<hr>
Conectar un cable ethernet al puerto ILO  
  
##### **Paso 2:**
<hr>
Encontrar la IP que el router o el servidor DHCP le ha proporcionado, esto se puede hacer desde el router o usando algún programa como [Angry IP Scanner](https://angryip.org/) en windows o [Fing](https://play.google.com/store/apps/details?id=com.overlook.android.fing&hl=es&gl=US) en android  

##### **Paso 3:**
<hr>
Hacer login en el dashboard de ILO usando la ip que hemos conseguido en el paso anterior.  
{% include figure.html path="assets/img/boot_ssd/1.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

##### **Paso 4:**
<hr>
Ir a **"Remote Console"** -> **"Remote Console"**  

Una vez estamos en Remote Console tendremos 2 opciones:  

- **"HTML5 Integrated Remote Console"** marcada en verde. Está opción solo nos saldrá si tenemos el servidor con la última versión de ILO disponible, en caso contrario no aparecerá.  
- **"Java Integrated Remote Console (Java IRC)"** marcada en naranja. Está opción nos saldrá tanto si tenemos la última versión de ILO como si no.  

{% include figure.html path="assets/img/boot_ssd/2.png" class="img-fluid rounded z-depth-1" zoomable=true %} 
En mi caso voy a seleccionar la consola de HTML 5, pero si no la tenéis disponible no pasa nada, podéis hacer lo mismo con la de Java.

##### **Paso 5:**
<hr>
Una vez tenemos una de las consolas abiertas encendemos el servidor y esperamos hasta que aparezca la siguiente pantalla:
{% include figure.html path="assets/img/boot_ssd/3.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Cuando aparezca presionamos **F10**, al pulsarlo quedará marcado en blanco como se puede apreciar en la foto anterior.

**En el caso de que la pantalla anterior no salga** deberíamos ver la siguiente:
{% include figure.html path="assets/img/boot_ssd/4.png" class="img-fluid rounded z-depth-1" zoomable=true %}  
Una vez la veamos pulsamos **F10**

##### **Paso 6:**
<hr>
Seleccionamos **"Perform Maintenance"**, marcado en verde.
{% include figure.html path="assets/img/boot_ssd/5.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

##### **Paso 7:**
<hr>
Seleccionamos la opción **"Hp Smart Storage Administrator (SSA)"**
{% include figure.html path="assets/img/boot_ssd/6.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

##### **Paso 8:**
<hr>
Creamos con el SSD un array de un solo disco con RAID 0 (todos los pasos necesarios seguidos) 

Seleccionamos la opción de **"RAID Dynamic Smart Array B120i"** marcada en verde.

{% include figure.html path="assets/img/boot_ssd/7.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Seleccionamos la opción de **"Configurar"** marcada en verde

{% include figure.html path="assets/img/boot_ssd/8.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Seleccionamos la opción de **"Crear array"** marcada en verde

{% include figure.html path="assets/img/boot_ssd/9.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Llegaremos a la siguiente ventana: 

{% include figure.html path="assets/img/boot_ssd/10.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

En está ventana tendremos que seleccionar el disco SSD desde el que queremos hacer BOOT (a mi no me aparece porque ya lo tengo creado con un raid 0) y pulsar **"Create Array"**

{% include figure.html path="assets/img/boot_ssd/11.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Al pulsar sobre **"Create Array"** saldrá la siguiente ventana:

{% include figure.html path="assets/img/boot_ssd/12.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

En esta ventana **seleccionamos RAID 0** y pulsamos sobre **"Create Logical Drive"** y a continuación saldrá la siguiente ventana:

{% include figure.html path="assets/img/boot_ssd/13.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Pulsamos **"Finish"** y ya tendríamos creado el RAID 0 sobre el ssd.

##### **Paso 9:**
<hr>
Una vez tenemos creado el RAID 0 tenemos que seleccionar **"Definir unidad/volumen de arranque"** dentro de las opciones de **"RAID Dynamic Smart Array B120i"**, marcado en verde.

{% include figure.html path="assets/img/boot_ssd/14.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Una vez lo hemos seleccionado tendremos la siguiente ventana:

{% include figure.html path="assets/img/boot_ssd/15.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

En esta ventana tendremos que seleccionar la **unidad lógica 1** como **unidad primaria de arranque** y pulsar **"Aceptar"**, selecciones marcadas en verde.

##### **Paso 10:**
<hr>
Reiniciamos el servidor y cuando cualquiera de las pantallas del **paso 5** vuelva a aparecer pulsamos **F9**

##### **Paso 11:**
<hr>
Una vez hemos pulsado **F9** accederemos a la BIOS del servidor y tenemos que ir al menú **"Standard Boot Order (IPL)"**

{% include figure.html path="assets/img/boot_ssd/16.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

##### **Paso 12:**
<hr>
Al seleccionar el menú **"Standard Boot Order (IPL)"** saldrá la siguiente pantalla:

{% include figure.html path="assets/img/boot_ssd/17.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

Aquí tendremos que seguir las indicaciones del pié de la BIOS para poner **IPL:1 como la primera opción** como se ve en la imagen.

##### **Paso 13:**
<hr>
Presionamos **ESC** para cerrar el menú y confirmamos los cambios pulsando **F10**.

{% include figure.html path="assets/img/boot_ssd/18.png" class="img-fluid rounded z-depth-1" zoomable=true %}  

<hr>

Y ya estaría, así podemos instalar cualquier SO en el SSD y hacer boot directamente desde el mismo.

Sólo unas puntualizaciones: 
- El puerto SATA de la placa base es **SATA 2**, por lo que no le vamos a sacar todo el partido al SSD que pongamos, pero como en mi caso es solo para hacer BOOT no me importa.
- Existe otro workaround que sería crear un USB con un bootloader desde el cual seleccionar desde que disco queremos arrancar pero me parece más engorroso.

Un saludo.  
Atte. ElPavlillo.