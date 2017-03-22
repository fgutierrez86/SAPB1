        ## Tablas de transacciones (Generan movimiento en OINM)

|Tabla         | Nombre                  |Transtype                             | Tabla de filas                     |
|--------------|-------------------------|--------------------------------------|------------------------------------|
|[OINM]()|Diario de almacen        | INM **58** - Inventory match         |                                    |
|[OINV](#4)|Facturas deudores        |INV **13** - Invoices                 |INV1 facturas deudores lineas       |
|**ORIN**      |Anulacion de facturas    |RIN **14** - Revert Invoices          |RIN1 anulacion de facturas linea    |
|**OPDN**      |Entrada de mercancías AP |PDN **20** - Purchase Delivery Notes  |PDN1 pedido de entrada de mercancias|
|**OIGN**      |Entrada de mercancías    |IGN **59** - Inventory General Entry  |IGN1                                |
|**OIGE**      |Salida de mercancías     |IGE **60** - Inventory General Exit   |IGE1 Entrada de mercancias lineas   |
|**OWTR**      |Traslado de stock        |WTR **67** - Warehouses Transfers     |WTR1 traslado de stock              |
|**OIPF**      |Costos de importación    |IPF 69 - Import file                  |IPF1 Precios de entrega             |


* __OINM Whse Journal -	Diario de almacen__ (stock movement table)
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

#4
###OINV (oInvoice) (ventas) Facturas deudores
    Transtype (object type):  
        INV **13** - Invoices
    * __INV1 Facturas deudores - lineas__

* __ORIN (oCreditNote) Nota de crédito de clientes (Anulacion de facturas)__ 
      Transtype (object type):
        RIN **14** - Revert Invoices
    * __RIN1 Anulacion de facturas Linea__  

* __OPDN (oPurchaseDeliveryNotes)  Entrada de mercancías__ 
      Transtype (object type):  
         PDN **20** - Purchase Delivery Notes
     * __PDN1	Pedido de entrada de mercancías - Filas__  
   Related to **OPCH**  

*  __OIGN (oInvetoryGenEntry) Entrada de mercancías__  
     A goods receipt in the Warehouse Management system (WMS) 
     is the physical inbound movement of goods or materials into the warehouse. 
     It is a goods movement that is used to post goods received from external 
     vendors or from in-plant production. All goods receipts result in an increase 
     of stock in the warehouse.

      Transtype (object type):
        IGN **59** - Inventory General Entry
     * __ IGN1	Entrada de mercancías: Líneas__  

* __OIGE ( oInvetoryGenExit ) Salida de mercancias__   
    A goods issuefrom Extended Warehouse Management (EWM) is a physical departure 
    of products from your warehouse. 
    With a goods issue posting, you reduce the stock in the warehouse. 
    You can use a goods issue to indicate goods deliveries to your customers.

    Transtype (object type):  
      IGE **60** - Inventory General Exit
    * __IGE1	Salida de mercancias - Lineas__

* __OWTR	Traslado de stocks__  
    You use this function to transfer inventory from one warehouse to another.

    Transtype (object type):  
        WTR **67** - Warehouses Transfers
    *  __WTR1	Traslado de stocks - Filas__
       
*  __OIPF	Costos de importación__  
    Transtype (object type):
        IPF 69 - Import file
    *  __IPF1	Precios de entrega - Líneas__


## otras tablas

*  __OPCH  Facturas de proveedores__  
   * PCH1  Factura de acreedor linea

## Tablas maestras

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

* Libro Ventas

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
    From OINV T0
        --Inner Join INV1 T1
        --On T0."DocEntry" = T1."DocEntry"
    Where T0."DocDate" Between '2017-02-01' And '2017-02-28'
    Order by T0."NumAtCard"
```

## Tipos de documentos

*  __IM :  Traspasos__
*  __OA :  Salidas de materiales__
*  __DI :  Precios de entrega para costos__
*  __RF :  Mall Ventura__
*  __EP :  Entrada de materiales__

