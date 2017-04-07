## TOC

* [TABLAS](#tablas)
   * [OINM](#oinm)
   * [OINV](#oinv)
   * [ORIN](#orin)
   * [OPDN](#opdn)
   * [OIGN](#oign)
   * [OIGE](#oige)
   * [OWTR](#owtr)
   * [OIPF](#oipf)
* [OTRAS TABLAS](#otras-tablas) 
* [CONSULTAS](#consultas) 
   * [Salidas por venta MV: TransType 13](#salidas)
   * [Devolucion de Stock : TransType 14](#devolucion)
   * [Entrada de Stock : TransType 59](#salidas)
   * [Salidas de Stock : TransType 60](#salidas)
   * [Traspasos de Stock : TransType 67](#salidas)
   * [Libro Ventas Mall Ventura](#salidas)
   * [Mall Ventura](#mall-ventura) 
* [Tipos de documentos](#tipos-de-documentos) 

##  TABLAS 
####     OINM - Object Type 58
[volver](#toc)
 Whse Journal - Diario de almacen (stock movement table)
    Transtype (object type):
      INM 58 - Inventory match

Table field: Transtype
       Values: 
    *  13     Salidas x venta MV Tipo documento RF 
    *  14     A/R Credit Memo
    *  20     Goods Receipt PO
    *  59     Goods Receipt 
    *  60     Goods Issue 
    *  67     Traspaso de inventario Tipo documento IM
    *  69     Landed Costs
    *  162    Inventory Valuation

####     OINV - salida por ventas MV Tipo 13
[volver](#toc) 
  *  (oInvoice) (ventas) Facturas deudores
  *  Transtype (object type): INV 13 Invoices
  *  INV1 Facturas deudores - lineas
  *  Tipo de documento : RF

![Imagen de Ventas](images/Compras.PNG)

```SQL
 Select
                        Cast(C."DocNum" as Varchar(10)) AS DOCNUM,
                        C."U_ESTADOFC" As ESTADO,
                        C."U_NROAUTOR" As NumeroAutorizacion,
                        C."NumAtCard" As NumeroFactura, 
                        C."U_CODCTRL",                
                        TO_CHAR(C."DocDate", 'dd/mm/yyyy') As Fecha,
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
        Where           C."DocDate" between '2017-03-01' And '2017-03-31' 
        and             A."TransType"=13 -- Facturas de venta del mall ventura 
        Order By        "U_NROAUTOR", "NumAtCard"



```

####     ORIN - Notas de crédito MV Tipo 14
[volver](#toc) 
(oCreditNote) Nota de crédito de clientes (Anulacion de facturas)
      Transtype (object type):
        RIN 14 - Revert Invoices
    * RIN1 Anulacion de facturas Linea

```SQL
Select
                Cast(C."DocNum" as Varchar(20)) As DOCNUM,
                C."U_NROAUTOR" As NumeroAutorizacion,
                C."NumAtCard" As NumeroFactura, 
                C."U_CODCTRL",
                --D."DocEntry",
                C."Comments",
                C."JrnlMemo",
                TO_CHAR(C."DocDate", 'dd/mm/yyyy') As Fecha,
                CC."ActId" As CuentaContable,
                CC."AcctName" As NombreCuentaContable,
                A."Warehouse",
                A."ItemCode",
                A."Dscription",
                A."InQty",
                A."OutQty",
                A."Currency",
                A."CalcPrice" As COSTO_UNITARIO,
                Abs(A."TransValue") As TransValue  
From            ORIN C  -- cabecera de la nota de credito (LV MALL)
Inner Join      RIN1  D  -- lineas de la nota de credito
ON              C."DocEntry" = D."DocEntry"
Inner Join      OINM A
ON              A."CreatedBy" = D."DocEntry" 
And             A."DocLineNum" = D."LineNum"
Inner Join      OACT CC -- Cuentas contables
ON              D."AcctCode" = CC."AcctCode"
Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
and             A."TransType"=14 -- Facturas 
Order By        "U_NROAUTOR", "NumAtCard"
```


####     OPDN - Entrada de mercancias por compra - Object Type 20
[volver](#toc) 
(oPurchaseDeliveryNotes)  Entrada de mercancías por compras locales 
    o importaciones.
    * Transtype (object type): PDN 20 - Purchase Delivery Notes
    * Tabla de Filas: PDN1  Pedido de entrada de mercancías - Filas
    * Tipo de documento: EP

![Imagen de Compras en el SAP B1](images/Compras.PNG)

```SQL
Select 
                        Cast(T0."BASE_REF" As Varchar(10)) As DOCNUM,
                        T0."DocLineNum",
                        Cast(T0."DocDate" As Date) As FechaContabilizacion,
                        T0."Warehouse",
                        T1."ActId",
						T1."AcctName",
                        T0."Comments",
                        T0."JrnlMemo",                      
                        T0."CardCode" As CODIGO_PROVEEDOR,
                        T0."CardName" As NOMBRE_PROVEEDOR,
                        T0."ItemCode",
                        T0."Dscription",
                        T0."InQty", 
                        T0."OutQty",
                        T0."Currency",
                        T0."CalcPrice" as PRECIO_UNITARIO,
                        Abs(T0."TransValue") As VALOR
        from            OINM T0 
        Inner Join 		OACT T1
        ON				T0."InvntAct" = T1."AcctCode"
        Where			T0."TransType" = 20 ---COMPRAS
        And 			T0."DocDate" Between '2017-01-01' And '2017-01-31'
        Order By		T0."BASE_REF", T0."DocLineNum"

```

###     OJDT Entrada de Diario (Asiento contable) Object Type 30





####     OIGN - Entrada por movimientos - Object Type 59
[volver](#toc) 
(oInvetoryGenEntry) Entrada de mercancías
     A goods receipt in the Warehouse Management system (WMS) 
     is the physical inbound movement of goods or materials into the warehouse. 
     It is a goods movement that is used to post goods received from external 
     vendors or from in-plant production. All goods receipts result in an increase 
     of stock in the warehouse.

   *  Transtype (object type):IGN 59 - Inventory General Entry
   *  Tabla de filas: IGN1 Entrada de mercancías
   *  Tipo de documentos: EM


```SQL
-- Entradas al CD
Select
          
                        C."DocNum",
                        --D."DocEntry",
                        D."LineNum",
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
        ON              A."CreatedBy" = D."DocEntry" 
        And             A."DocLineNum" = D."LineNum"
        Inner           Join OACT CC -- Cuentas contables
        ON              D."AcctCode" = CC."AcctCode"
        Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
        and             A."TransType"=59 -- Entradas  
        And             "Warehouse" <> 'AMVEN'
        Order By		C."DocNum", D."LineNum"


-- Entradas al Mall Ventura:

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
        ON              A."CreatedBy" = D."DocEntry" 
        And             A."DocLineNum" = D."LineNum"
        Inner           Join OACT CC -- Cuentas contables
        ON              D."AcctCode" = CC."AcctCode"
        Where           C."DocDate" between '2017-01-01' And '2017-01-31' 
        and             A."TransType"=59 -- Facturas 
        And             "Warehouse" = 'AMVEN'



```

####     OIGE - Salida de mercancias - Object type 60
[volver](#toc) 
( oInvetoryGenExit ) Salida de mercancias 
    A goods issuefrom Extended Warehouse Management (EWM) is a physical departure 
    of products from your warehouse. 
    With a goods issue posting, you reduce the stock in the warehouse. 
    You can use a goods issue to indicate goods deliveries to your customers.

    Transtype (object type):
      IGE **60** - Inventory General Exit
    * IGE1    Salida de mercancias - Lineas

```SQL

 Select
                        Cast(T0."BASE_REF" As Varchar(20)) As DOCUMENTO,
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
                        Between '2017-01-01' And '2017-01-30'
        And             T0."TransType" = 60 -- SALIDAS
        And             T0."Warehouse" = 'AMVEN'
       Order by        "BASE_REF", "DocLineNum", "ActId"

 Select
                        Cast(T0."BASE_REF" As Varchar(20)) As DOCUMENTO,
                        T2."U_NPedidoGera" AS PEDIDOGERA,
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
        Inner Join		IGE1 T2
        On				T0."CreatedBy" = T2."DocEntry"
        And				T0."DocLineNum" = T2."LineNum"
        Where           T0."DocDate"
        Between         '2017-02-01' And '2017-02-28'
        And             T0."TransType" = 60 -- SALIDAS
        And             T0."Warehouse" <> 'AMVEN'
		Order by        "BASE_REF", "DocLineNum", "ActId"  


```

####     OWTR - Traslado de Stock - Object Type 67
[volver](#toc) 
  Traslado de stocks
    You use this function to transfer inventory from one warehouse to another.

    Transtype (object type):
        WTR **67** - Warehouses Transfers
    *  WTR1    Traslado de stocks - Filas

```SQL


Select 
                        Cast((T0."DocNum") As Varchar(10)) As DOCNUM,
                        T1."DocLineNum",
                        T0."Comments",
                        T0."JrnlMemo",
                        --Cast(T0."DocEntry" as Varchar(10)) As DOCENTRY, 
                        T2."FromWhsCod" As Origen, 
                        T2."WhsCode" As Destino, 
                        Cast(T0."DocDate" As Date) As FechaContabilizacion,
                        T1."ItemCode",
                        T1."Dscription",
                        T1."OutQty" as Cantidad,
                        T1."CalcPrice" As COSTO_UNITARIO,
                        Abs(T1."TransValue") As Transvalue
from                    OWTR T0 
Inner Join              WTR1 T2
ON                      T0."DocEntry" = T2."DocEntry" 
Inner Join              OINM T1
ON                      T0."DocNum" = T1."BASE_REF" 
And                     T1."InQty" = 0 --- SALIDAS
And                     T1."CreatedBy" = T2."DocEntry" 
And                     T1."DocLineNum" = T2."LineNum"  -- 
Where                   T1."TransType" = 67
And 			        T0."DocDate" between '2017-02-01' And '2017-02-28'
And                     (T2."FromWhsCod" = 'AMVEN' or T2."WhsCode" = 'AMVEN') -- Mall Ventura
--And                     (T2."FromWhsCod" <> 'AMVEN' And T2."WhsCode" <> 'AMVEN') -- Resto
Order By		        T0."DocNum", T1."DocLineNum"


```

--44339   223571####     OIPF - Costos de importación - Object Type 69
[volver](#toc) 
   Costos de importación
    Transtype (object type):
        IPF 69 - Import file
    *  IPF1    Precios de entrega - Líneas



OAC## Otras Tablas
[volver](#toc)

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

####         Libro Ventas 
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







[volver](#toc) 

*  IM :  Traspasos
*  OA :  Salidas de materiales
*  DI :  Precios de entrega para costos
*  RF :  Facturas del Mall Ventura
*  EP :  Entrada de materiales
*  RC :  Notas de crédito, anulacion de facturas Mall Ventura


