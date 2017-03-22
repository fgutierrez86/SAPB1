## Tablas
* __OINM	Diario de almacen__ (stock movement table)
    Transtype (object type):    
      INM 58 - Inventory match

    Table field: Transtype  
       Values:

    *  __13 	A/R Invoice__  
    *  __14 	A/R Credit Memo__  
    *  __20 	Goods Receipt PO__  
    *  __59     Goods Receipt__  
             A goods receipt in the Warehouse Management system (WMS) 
             is the physical inbound movement of goods or materials into the warehouse. 
             It is a goods movement that is used to post goods received from external 
             vendors or from in-plant production. All goods receipts result in an increase 
             of stock in the warehouse.
    * __60 	Goods Issue__  
             A goods issuefrom Extended Warehouse Management (EWM) is a physical departure of products from your warehouse. 
             With a goods issue posting, you reduce the stock in the warehouse. 
             You can use a goods issue to indicate goods deliveries to your customers.
             Salida de productos del warehouse (almacen)
    * __67 	Inventory Transfers__  
            You use this function to transfer inventory from one warehouse to another.
    * __162	Inventory Valuation__  
    * __69	Landed Costs__  


* __OIGE	Salida de mercancias__
    Transtype (object type):  
      IGE 60 - Inventory General Exit
    * __IGE1	Salida de mercancias - Lineas__


* __OJDT Asiento__
    Transtype (object type):  
      JDT 30 - Journal Transactions
    * __JDT1 Asiento Fila__

* __OINV Facturas deudores__
    Transtype (object type):  
        INV 13 - Invoices
    * __INV1 Facturas deudores - lineas__


* __OWTR	Traslado de stocks__  
    Transtype (object type):  
        WTR 67 - Warehouses Transfers
    *  __WTR1	Traslado de stocks - Filas__
       


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



