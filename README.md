## Tablas de transacciones 

(Generan movimiento en OINM)

|Tabla | Nombre                           |Transtype                             | Tabla de filas                     |
|------|----------------------------------|--------------------------------------|------------------------------------|
|[OINM]|Diario de almacen                 |INM **58** - Inventory match          |                                    |
|[OINV]|Facturas deudores (ventas MV)     |INV **13** - Invoices                 |INV1 facturas deudores lineas       |
|[ORIN]|Anulacion de facturas(MV)         |RIN **14** - Revert Invoices          |RIN1 anulacion de facturas linea    |
|[OPDN]|Entrada de mercanc�as AP(Import.) |PDN **20** - Purchase Delivery Notes  |PDN1 pedido de entrada de mercancias|
|[OIGN]|Entrada de mercanc�as             |IGN **59** - Inventory General Entry  |IGN1                                |
|[OIGE]|Salida de mercanc�as              |IGE **60** - Inventory General Exit   |IGE1 Entrada de mercancias lineas   |
|[OWTR]|Traslado de stock                 |WTR **67** - Warehouses Transfers     |WTR1 traslado de stock              |
|[OIPF]|Costos de importaci�n             |IPF 69 - Import file                  |IPF1 Precios de entrega             |

* [Otras tablas](#markdown-header-otras-tablas)
* [Tablas maestras](#markdown-header-tablas-maestras) 
* [Queries](#markdown-header-queries) 
   *  [Mall Ventura](#markdown-header-mall-ventura) 
* [Tipos de documentos](#markdown-header-tipos-de-documentos) 


###     OINM        ###
[volver](#markdown-header-tablas-de-transacciones)
 Whse Journal -	Diario de almacen (stock movement table)
    Transtype (object type):
      INM 58 - Inventory match

    Table field: Transtype  
       Values: 
    *  13 	A/R Invoice  
    *  14 	A/R Credit Memo  
    *  20 	Goods Receipt PO  
    *  59     Goods Receipt 
    *  60 	Goods Issue 
    *  67 	Inventory Transfers  
    *  69	Landed Costs  
    *  162	Inventory Valuation  

###     OINV        ### 
[volver](#markdown-header-tablas-de-transacciones)   
__(oInvoice) (ventas) Facturas deudores__
    Transtype (object type):  
        INV **13** - Invoices
    * __INV1 Facturas deudores - lineas__

###     ORIN        ### 
[volver](#markdown-header-tablas-de-transacciones)   
__(oCreditNote) Nota de cr�dito de clientes (Anulacion de facturas)__ 
      Transtype (object type):
        RIN **14** - Revert Invoices
    * __RIN1 Anulacion de facturas Linea__  

###     OPDN        ### 
[volver](#markdown-header-tablas-de-transacciones)   
__(oPurchaseDeliveryNotes)  Entrada de mercanc�as__ 
      Transtype (object type):  
         PDN **20** - Purchase Delivery Notes
     * __PDN1	Pedido de entrada de mercanc�as - Filas__  
   Related to **OPCH**  

###     OIGN        ### 
[volver](#markdown-header-tablas-de-transacciones)   
(oInvetoryGenEntry) Entrada de mercanc�as
     A goods receipt in the Warehouse Management system (WMS) 
     is the physical inbound movement of goods or materials into the warehouse. 
     It is a goods movement that is used to post goods received from external 
     vendors or from in-plant production. All goods receipts result in an increase 
     of stock in the warehouse.

      Transtype (object type):
        IGN **59** - Inventory General Entry
     *  IGN1	Entrada de mercanc�as: L�neas  

###     OIGE        ### 
[volver](#markdown-header-tablas-de-transacciones)   
__( oInvetoryGenExit ) Salida de mercancias__   
    A goods issuefrom Extended Warehouse Management (EWM) is a physical departure 
    of products from your warehouse. 
    With a goods issue posting, you reduce the stock in the warehouse. 
    You can use a goods issue to indicate goods deliveries to your customers.

    Transtype (object type):  
      IGE **60** - Inventory General Exit
    * __IGE1	Salida de mercancias - Lineas__

###     OWTR        ###
[volver](#markdown-header-tablas-de-transacciones)   
  __Traslado de stocks__  
    You use this function to transfer inventory from one warehouse to another.

    Transtype (object type):  
        WTR **67** - Warehouses Transfers
    *  __WTR1	Traslado de stocks - Filas__
       
###     OIPF        ###	
[volver](#markdown-header-tablas-de-transacciones)   
   __Costos de importaci�n__  
    Transtype (object type):
        IPF 69 - Import file
    *  __IPF1	Precios de entrega - L�neas__


## otras tablas     ###
[volver](#markdown-header-tablas-de-transacciones)   

*  __OPCH  Facturas de proveedores__  
   * PCH1  Factura de acreedor linea

## Tablas maestras      ###
[volver](#markdown-header-tablas-de-transacciones)   

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


## Queries      ###
[volver](#markdown-header-tablas-de-transacciones)   


```SQL


Select sum("TransValue") from 
(
        SELECT 
            "TransType", "Warehouse", "CreatedBy", "BASE_REF", "ItemCode", 
            "InQty", "OutQty", "Price", "CalcPrice", "TransValue", 
            "PriceDiff", "RevalTotal", "VarVal", "NegInvAdjs", 
            "OpenNegInv", "WipVal", "InvntAct", "CreateDate", 
            "TransSeq", "ApplObj", "AppObjAbs", "AppObjType", 
            "AppObjLine", "CostMethod"
        FROM OINM
        Where "TransType" = 59  -- 59 Entradas
        And "BASE_REF" = 98

        -- Where "TransType"=60  -- Salida
        -- And "BASE_REF" = 363

        --Where "TransType" = 67  -- Traspaso
        --And "BASE_REF" = 1787
        --And "TransValue" > 0
)


Select  "TransType", Count(*) As Cantidad From OINM
Group by "TransType"


Select
        'Ingresos' as TipMov,
        --'Salidas' as TipMov,
        Cast(T0."DocDate" as date) AS FECHA,
        cast(T0."DocNum" as varchar) As Numero,
        T3."ActId" AS CUENTA_CONTABLE,
        T3."AcctName" AS DESC_CONTABLE,
        T1."ItemCode" AS COD_MATERIAL,
        T1."Dscription" As PRODUCTO,
        T1."Quantity" as CANTIDAD,
        abs(T2."TransValue") as TOTAL,
        'SAP' as NATURALEZA,
        'SAP' as FAMILIA,
        T1."U_NPedidoGera" as PEDIDO,
        T1."WhsCode" as ALMACEN,
        T0."Comments" as Comentario, 
        T0."JrnlMemo" as Observaciones
From OIGN T0  -- Ingresos
Inner Join IGN1 T1 -- Ingresos
--From OIGE T0 -- Salidas
--Inner Join IGE1 T1 -- salidas
on T0."DocEntry"=T1."DocEntry"
inner join OINM T2  -- diarios de inventario.
On 
	T1."DocEntry"=T2."CreatedBy" and 
	T0."DocNum"=T2."BASE_REF" and 	
	T0."ObjType"=T2."TransType" and 
	T1."ItemCode"=T2."ItemCode" and 
	T1."LineNum"=T2."DocLineNum"
Inner Join OACT T3 -- Cuentas contables.
ON T1."AcctCode" = T3."AcctCode"
Where Cast(T0."DocDate" as Date) between '2017-02-01' and '2017-02-28'
and T1."U_NPedidoGera" 
--- Estos pedidos de febrero estan mal ingresado en febrero.(comentar para otros
-- meses.
not in (1178689,1178724,1178749,1178781,1178842,1178844,1178855,1178898,1178971,1179020)
order by Cast(T0."DocDate" as Date),cast(T0."DocNum" as varchar)

```

## CONSULTAS        ###

###     Salidas por venta MV: TransType 13      ###

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
###     Entrada de Stock : TransType 59         ###  

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

###     Salidas de Stock : TransType 60     ###
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





###     Traspasos de Stock : TransType 67       ###
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

###     Libro Ventas Mall Ventura       ###
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

## Tipos de documentos      ###
[volver](#markdown-header-tablas-de-transacciones)   

*  __IM :  Traspasos__
*  __OA :  Salidas de materiales__
*  __DI :  Precios de entrega para costos__
*  __RF :  Facturas del Mall Ventura__
*  __EP :  Entrada de materiales__
*  __RC :  Notas de cr�dito, anulacion de facturas Mall Ventura__


