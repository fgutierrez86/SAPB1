## Tablas de transacciones 
----------------------------------
__(Generan movimiento en OINM)__

|Tabla                         | Nombre                                  |Transtype                             | Tabla de filas                     |
|------------------------------|-----------------------------------------|--------------------------------------|------------------------------------|
|[OINM]()                      |Diario de almacen                        | INM **58** - Inventory match         |                                    |
|[OINV](#markdown-header-oinv) |Facturas deudores (ventas MALL Ventura)  |INV **13** - Invoices                 |INV1 facturas deudores lineas       |
|[ORIN](#markdown-header-orin) |Anulacion de facturas(Mall Ventura)      |RIN **14** - Revert Invoices          |RIN1 anulacion de facturas linea    |
|[OPDN](#markdown-header-opdn) |Entrada de mercancías AP(Importaciones)  |PDN **20** - Purchase Delivery Notes  |PDN1 pedido de entrada de mercancias|
|[OIGN](#markdown-header-oign) |Entrada de mercancías                    |IGN **59** - Inventory General Entry  |IGN1                                |
|[OIGE](#markdown-header-oige) |Salida de mercancías                     |IGE **60** - Inventory General Exit   |IGE1 Entrada de mercancias lineas   |
|[OWTR](#markdown-header-owtr) |Traslado de stock                        |WTR **67** - Warehouses Transfers     |WTR1 traslado de stock              |
|[OIPF](#markdown-header-oipf) |Costos de importación                    |IPF 69 - Import file                  |IPF1 Precios de entrega             |

* [Otras tablas](#markdown-header-otras-tablas)  
* [Tablas maestras](#markdown-header-tablas-maestras) 
* [Queries](#markdown-header-queries) 
   * [Libro Ventas](#markdown-header-libro-ventas) 
* [Tipos de documentos](#markdown-header-tipos-de-documentos) 


### OINM 
[<--](#markdown-header-tablas-de-transacciones)   
__Whse Journal -	Diario de almacen (stock movement table)__
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

### OINV 
[<--](#markdown-header-tablas-de-transacciones)   
__(oInvoice) (ventas) Facturas deudores__
    Transtype (object type):  
        INV **13** - Invoices
    * __INV1 Facturas deudores - lineas__

### ORIN 
[<--](#markdown-header-tablas-de-transacciones)   
__(oCreditNote) Nota de crédito de clientes (Anulacion de facturas)__ 
      Transtype (object type):
        RIN **14** - Revert Invoices
    * __RIN1 Anulacion de facturas Linea__  

### OPDN 
[<--](#markdown-header-tablas-de-transacciones)   
__(oPurchaseDeliveryNotes)  Entrada de mercancías__ 
      Transtype (object type):  
         PDN **20** - Purchase Delivery Notes
     * __PDN1	Pedido de entrada de mercancías - Filas__  
   Related to **OPCH**  

### OIGN 
[<--](#markdown-header-tablas-de-transacciones)   
__(oInvetoryGenEntry) Entrada de mercancías__  
     A goods receipt in the Warehouse Management system (WMS) 
     is the physical inbound movement of goods or materials into the warehouse. 
     It is a goods movement that is used to post goods received from external 
     vendors or from in-plant production. All goods receipts result in an increase 
     of stock in the warehouse.

      Transtype (object type):
        IGN **59** - Inventory General Entry
     * __ IGN1	Entrada de mercancías: Líneas__  

### OIGE 
[<--](#markdown-header-tablas-de-transacciones)   
__( oInvetoryGenExit ) Salida de mercancias__   
    A goods issuefrom Extended Warehouse Management (EWM) is a physical departure 
    of products from your warehouse. 
    With a goods issue posting, you reduce the stock in the warehouse. 
    You can use a goods issue to indicate goods deliveries to your customers.

    Transtype (object type):  
      IGE **60** - Inventory General Exit
    * __IGE1	Salida de mercancias - Lineas__

### OWTR
[<--](#markdown-header-tablas-de-transacciones)   
  __Traslado de stocks__  
    You use this function to transfer inventory from one warehouse to another.

    Transtype (object type):  
        WTR **67** - Warehouses Transfers
    *  __WTR1	Traslado de stocks - Filas__
       
### OIPF	
[<--](#markdown-header-tablas-de-transacciones)   
   __Costos de importación__  
    Transtype (object type):
        IPF 69 - Import file
    *  __IPF1	Precios de entrega - Líneas__


## otras tablas
[<--](#markdown-header-tablas-de-transacciones)   

*  __OPCH  Facturas de proveedores__  
   * PCH1  Factura de acreedor linea

## Tablas maestras
[<--](#markdown-header-tablas-de-transacciones)   

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


## Queries
[<--](#markdown-header-tablas-de-transacciones)   


```SQL
-- Libro Ventas
    Select 
            T0."NumAtCard" As NumeroFactura, 
            T0."DocTotal" As Total, 
            T0."DocDate" As Fecha, 
            T0."U_NIT" As Nit, 
            T0."U_RAZSOC" As RazonSocial, 
            T0."U_CODCTRL" As CodigoControl, 
            T0."U_NROAUTOR"  As NumeroAutorizacion,
            T0."JrnlMemo" 
    From OINV T0
        --Inner Join INV1 T1
        --On T0."DocEntry" = T1."DocEntry"
    Where T0."DocDate" Between '2017-02-01' And '2017-02-28'
    Order by T0."NumAtCard"

-- Consulta de movimientos


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

-- Contar los tipos de transacciones

Select  "TransType", Count(*) As Cantidad From OINM
Group by "TransType"

-- Calculo de Salidas/Entradas de mercancias

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

## Tipos de documentos
[<--](#markdown-header-tablas-de-transacciones)   

*  __IM :  Traspasos__
*  __OA :  Salidas de materiales__
*  __DI :  Precios de entrega para costos__
*  __RF :  Mall Ventura__
*  __EP :  Entrada de materiales__


