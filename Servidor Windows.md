### Lista de usuarios en el servidor 192.168.10.200:

```SH

C:\WINDOWS\system32>qwinsta /server:192.168.15.200
 NOMBRE DE SESIÓN  NOMBRE DE USUARIO        ID  ESTADO    TIPO   DISPOSITIVO
 services                                    0  Desc
 console                                     1  Conn
                   sap                       2  Desc
                   SAP2                      3  Desc
 rdp-tcp#80        jcalderon                 5  Activo
                   ljimenez                  8  Desc
 rdp-tcp#76        yzlatar                   9  Activo
                   ccoro                    10  Desc
                   sap3                     13  Desc
 rdp-tcp#78        fgutierrez               14  Activo
                   dmendez                  15  Desc
 rdp-tcp#11        ebanegass                18  Activo
                   marcoalt                 19  Desc
 rdp-tcp                                 65536  Escuchar


# Reset y desconecta la sesion con ID = 16:


C:\WINDOWS\system32>rwinsta 16 /server:192.168.15.200

```
