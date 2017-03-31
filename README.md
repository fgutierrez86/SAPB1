## Tablas de transacciones 

(Generan movimiento en OINM)

|Tabla | Nombre                           |Transtype                             | Tabla de filas                     |
|------|----------------------------------|--------------------------------------|------------------------------------|
|[OINM]|Diario de almacen                 |INM **58** - Inventory match          |                                    |
|[OINV]|Facturas deudores (ventas MV)     |INV **13** - Invoices                 |INV1 facturas deudores lineas       |
|[ORIN]|Anulacion de facturas(MV)         |RIN **14** - Revert Invoices          |RIN1 anulacion de facturas linea    |
|[OPDN]|Entrada de mercancías AP(Import.) |PDN **20** - Purchase Delivery Notes  |PDN1 pedido de entrada de mercancias|
|[OIGN]|Entrada de mercancías             |IGN **59** - Inventory General Entry  |IGN1                                |
|[OIGE]|Salida de mercancías              |IGE **60** - Inventory General Exit   |IGE1 Entrada de mercancias lineas   |
|[OWTR]|Traslado de stock                 |WTR **67** - Warehouses Transfers     |WTR1 traslado de stock              |
|[OIPF]|Costos de importación             |IPF 69 - Import file                  |IPF1 Precios de entrega             |

* [Otras tablas](#otras-tablas)
* [Tablas maestras](#tablas-maestras) 
* [Queries](#queries) 
   *  [Mall Ventura](#mall-ventura) 
* [Tipos de documentos](#tipos-de-documentos) 


###     OINM
[volver](tablas-de-transacciones)
 Whse Journal -    Diario de almacen (stock movement table)
    Transtype (object type):
      INM 58 - Inventory match

Table field: Transtype
       Values: 
    *  13     A/R Invoice  
    *  14     A/R Credit Memo  
    *  20     Goods Receipt PO  
    *  59     Goods Receipt 
    *  60     Goods Issue 
    *  67     Inventory Transfers  
    *  69    Landed Costs  
    *  162    Inventory Valuation  

###     OINV
[volver](#tablas-de-transacciones)   
__(oInvoice) (ventas) Facturas deudores__
    Transtype (object type):  
        INV **13** - Invoices
    * __INV1 Facturas deudores - lineas__

###     ORIN
[volver](#tablas-de-transacciones)   
__(oCreditNote) Nota de crédito de clientes (Anulacion de facturas)__ 
      Transtype (object type):
        RIN **14** - Revert Invoices
    * __RIN1 Anulacion de facturas Linea__  

###     OPDN
[volver](#tablas-de-transacciones)   
__(oPurchaseDeliveryNotes)  Entrada de mercancías__ 
      Transtype (object type):  
         PDN **20** - Purchase Delivery Notes
     * __PDN1    Pedido de entrada de mercancías - Filas__  
   Related to **OPCH**  

###     OIGN
[volver](#tablas-de-transacciones)   
(oInvetoryGenEntry) Entrada de mercancías
     A goods receipt in the Warehouse Management system (WMS) 
     is the physical inbound movement of goods or materials into the warehouse. 
     It is a goods movement that is used to post goods received from external 
     vendors or from in-plant production. All goods receipts result in an increase 
     of stock in the warehouse.

      Transtype (object type):
        IGN **59** - Inventory General Entry
     *  IGN1    Entrada de mercancías: Líneas  

###     OIGE
[volver](#tablas-de-transacciones)   
__( oInvetoryGenExit ) Salida de mercancias__   
    A goods issuefrom Extended Warehouse Management (EWM) is a physical departure 
    of products from your warehouse. 
    With a goods issue posting, you reduce the stock in the warehouse. 
    You can use a goods issue to indicate goods deliveries to your customers.

    Transtype (object type):  
      IGE **60** - Inventory General Exit
    * __IGE1    Salida de mercancias - Lineas__

###     OWTR
[volver](#tablas-de-transacciones)   
  __Traslado de stocks__  
    You use this function to transfer inventory from one warehouse to another.

    Transtype (object type):  
        WTR **67** - Warehouses Transfers
    *  __WTR1    Traslado de stocks - Filas__
       
###     OIPF
[volver](#tablas-de-transacciones)   
   __Costos de importación__  
    Transtype (object type):
        IPF 69 - Import file
    *  __IPF1    Precios de entrega - Líneas__


## otras tablas
[volver](#tablas-de-transacciones)   

*  __OPCH  Facturas de proveedores__  
   * PCH1  Factura de acreedor linea

## Tablas maestras
[volver](#tablas-de-transacciones)   

```SQL

/* Chart of Accounts */
SELECT * FROM OACT

/* Item Master */
SELECT * FROM OITM

/* Employee Master (HR) */
SELECT * FROM OHEM

/* Incomming Payments and the line level Information */
SELECT * FROM ORCT

```


## CONSULTAS 

###     Salidas por venta MV: TransType 13    

```SQL
        Select  
                           C."DocNum",
                           C."U_ESTADOFC" As ESTADO,
                        C."U_NROAUTOR" As NumeroAutorizacion,
                        C."NumAtCard" As NumeroFactura, 
                        C."U_CODCTRL",                  
                        C."DocDate" As Fecha,
                        CC."ActId" As CuentaContable,
                        CC."AcctName" As NombreCuentaContable,
                        A."Warehouse" As ALMACEN,
                        A."ItemCode",
                        A."Dscription",
                        A."InQty",
                        A."OutQty",
                        A."Currency",
                        A."CalcPrice" as COSTO_UNITARIO,
                        Abs(A."TransValue") As TransValue    
        From            OINV C  -- cabecera de la venta (LV MALL)
        Inner Join      INV1  D  -- lineas de la venta
        ON              C."DocEntry" = D."DocEntry"
        Inner Join      OINM A
        ON              A."CreatedBy" = D."DocEntry" And A."DocLineNum" = D."LineNum"
        Inner Join      OACT CC -- Cuentas contables
        ON              D."AcctCode" = CC."AcctCode"
        Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
        and             A."TransType"=13 -- Facturas de venta del mall ventura 
        
        Order By        "U_NROAUTOR", "NumAtCard"
```
###     Devolucion de Stock : TransType 14      ###

```SQL
        Select  
                        C."U_NROAUTOR" As NumeroAutorizacion,
                        C."NumAtCard" As NumeroFactura, 
                        C."U_CODCTRL",
                        D."DocEntry",
                        C."Comments",
                        C."JrnlMemo",
                        C."DocDate" As Fecha,
                        CC."ActId" As CuentaContable,
                        CC."AcctName" As NombreCuentaContable,
                        A."Warehouse",
                        A."ItemCode",
                        A."Dscription",
                        A."InQty",
                        A."OutQty",
                        A."Currency",
                        Abs(A."TransValue") As TransValue    
        From            ORIN C  -- cabecera de la nota de credito (LV MALL)
        Inner Join      RIN1  D  -- lineas de la nota de credito
        ON              C."DocEntry" = D."DocEntry"
        Inner Join      OINM A
        ON              A."CreatedBy" = D."DocEntry" And A."DocLineNum" = D."LineNum"
        Inner Join      OACT CC -- Cuentas contables
        ON              D."AcctCode" = CC."AcctCode"
        Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
        and             A."TransType"=14 -- Facturas 
        Order By        "U_NROAUTOR", "NumAtCard"
```
###     Entrada de Stock : TransType 59

```SQL
        Select  
            
                        C."DocNum",
                        D."DocEntry",
                        C."Comments",
                        C."JrnlMemo",
                        C."DocDate" As Fecha,
                        CC."ActId" As CuentaContable,
                        CC."AcctName" As NombreCuentaContable,
                        A."Warehouse",
                        A."ItemCode",
                        A."Dscription",
                        A."InQty",
                        A."OutQty",
                        A."Currency",
                        A."CalcPrice" As PRECIO_UNITARIO,
                        Abs(A."TransValue") As VALOR
        From            OIGN C  -- Cabecera Entrada de Mercancias
        Inner Join      IGN1  D  -- lineas de la Entrada de mercancias
        ON              C."DocEntry" = D."DocEntry"
        Inner Join      OINM A  --- DIARIO DE ALMACENCES...
        ON              A."CreatedBy" = D."DocEntry" And A."DocLineNum" = D."LineNum"
        Inner           Join OACT CC -- Cuentas contables
        ON              D."AcctCode" = CC."AcctCode"
        Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
        and             A."TransType"=59 -- Facturas 
        And             "Warehouse" = 'AMVEN'
```

###     Salidas de Stock : TransType 60
```SQL
    
    Select  
                        T0."BASE_REF" As DOCUMENTO,
                        T0."DocLineNum" As LINEA,
                        T0."Warehouse" As ALMACEN,
                        T1."ActId" AS CUENTA_CONTABLE,
                        T1."AcctName" AS NOMBRE_CUENTA_CONTABLE,
                        Cast(T0."DocDate" As Date) As FECHA_DOC,
                        T0."Comments",
                        T0."JrnlMemo",
                        T0."ItemCode",
                        T0."Dscription",
                        T0."InQty",
                        T0."OutQty",
                        T0."CalcPrice" As COSTO_UNITARIO,
                        abs(T0."TransValue") as VALOR 
        from            OINM T0
        Inner Join      OACT T1
        On              T0."CardCode" = T1."AcctCode"
        Where           T0."DocDate"  
                        Between '2017-01-01' And '2017-03-29'
                        And T0."TransType" = 60
                        And T1."Segment_0" =  '5230701' 
       Order by        "BASE_REF", "DocLineNum", "ActId"
  
```





###     Traspasos de Stock : TransType 67
```SQL

        Select 
                        T0."DocNum",
                        T1."DocLineNum",
                        T0."Comments",
                        T0."JrnlMemo",
                        T0."DocEntry", 
                        T2."FromWhsCod" As Origen, 
                        T2."WhsCode" As Destino, 
                        Cast(T0."DocDate" As Date) As FechaContabilizacion,
                        T1."ItemCode",
                        T1."Dscription",
                        T1."OutQty" as Cantidad,
                        Abs(T1."TransValue") As Transvalue
        from            OWTR T0 
        Inner           Join WTR1 T2
        ON              T0."DocEntry" = T2."DocEntry" 
        Inner Join      OINM T1
        ON              T0."DocNum" = T1."BASE_REF" And T1."InQty" = 0 --- SALIDAS
        And             T1."CreatedBy" = T2."DocEntry" and T1."DocLineNum" = T2."LineNum"  -- 
        Where           T1."TransType" = 67
        And             (T2."FromWhsCod" = 'AMVEN' or T2."WhsCode" = 'AMVEN')


```

###     Libro Ventas Mall Ventura
```SQL
        Select 
                        T0."NumAtCard" As NumeroFactura, 
                        T0."DocTotal" As Total, 
                        T0."DocDate" As Fecha, 
                        T0."U_NIT" As Nit, 
                        T0."U_RAZSOC" As RazonSocial, 
                        T0."U_CODCTRL" As CodigoControl, 
                        T0."U_NROAUTOR"  As NumeroAutorizacion,
                        T0."JrnlMemo" 
        From            OINV T0
        --Inner Join    INV1 T1
        --On            T0."DocEntry" = T1."DocEntry"
        Where           T0."DocDate" Between '2017-02-01' And '2017-02-28'
        Order by        T0."NumAtCard"
```

## Tipos de documentos
[volver](#tablas-de-transacciones)   

*  __IM :  Traspasos__
*  __OA :  Salidas de materiales__
*  __DI :  Precios de entrega para costos__
*  __RF :  Facturas del Mall Ventura__
*  __EP :  Entrada de materiales__
*  __RC :  Notas de crédito, anulacion de facturas Mall Ventura__


