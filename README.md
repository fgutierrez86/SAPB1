## Tablas
* __OINM	Diario de almacén__ 

    Tipos de TransType que existen en la bd Natura

        * __13 	A/R Invoice__
        * __14 	A/R Credit Memo__
        * __20 	Goods Receipt PO__
        * __59 	Goods Receipt__  A goods receipt in the Warehouse Management system (WMS) 
            is the physical inbound movement of goods or materials into the warehouse. 
            It is a goods movement that is used to post goods received from external 
            vendors or from in-plant production. All goods receipts result in an increase 
            of stock in the warehouse.
        * __60 	 Goods Issue__
            Salida de productos del warehouse (almacen)
        * __67 	Inventory Transfers__
            You use this function to transfer inventory from one warehouse to another.
        * __162	Inventory Valuation__
        * __69	Landed Costs__


* __OIGE	Salida de mercancías__
* __IGE1	Salida de mercancías - Líneas__


* __OJDT Asiento__
* __JDT1 Asiento Fila__

* __OINV Facturas deudores__
* __INV1 Facturas deudores - lineas__


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



