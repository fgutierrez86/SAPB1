## Tablas
### OINM	Diario de almacén  

 Tipos de TransType que existen en la bd Natura

- ** 13 	A/R Invoice **
- ** 14 	A/R Credit Memo **
- ** 20 	Goods Receipt PO **
- ** 59 	Goods Receipt **  A goods receipt in the Warehouse Management system (WMS) 
	is the physical inbound movement of goods or materials into the warehouse. 
	It is a goods movement that is used to post goods received from external 
	vendors or from in-plant production. All goods receipts result in an increase 
	of stock in the warehouse.
- ** 60 	 Goods Issue **    
    Salida de productos del warehouse (almacen)
- ** 67 	Inventory Transfers**  
    You use this function to transfer inventory from one warehouse to another.
- ** 162	Inventory Valuation **
- ** 69	Landed Costs **


### OIGE	Salida de mercancías
### IGE1	Salida de mercancías - Líneas


### OJDT Asiento
### JDT1 Asiento Fila

### OINV Facturas deudores
### INV1 Facturas deudores - lineas


## Queries

### Libro Ventas

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



