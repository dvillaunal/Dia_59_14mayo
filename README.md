```{r, eval=FALSE, include=TRUE}
"Protocolo:

1. Daniel felipe Villa Rengifo

2. Lenguaje: R

3. Tema: Formas de eliminar la informacion que falta o remplazarla

4. Fuentes:
   https://cran.r-project.org/web/packages/AmesHousing/AmesHousing.pdf"
```


Este replit es en remplazo al de regresión lineal, cambiando el tema por:

> Formas de eliminar la informacion que falta o remplazarla

Trabajaremos con un archivo csv, con valores faltantes o NA

```{r}
# lectura de la base de datos 
housing.data <- read.csv("housing-with-missing-value.csv",
                         header = TRUE, stringsAsFactors = FALSE)

# resumen para visualizar la base de datos 

summary(housing.data)


# En el anterior resumen podemos ver las siguientes variables contiene NA:
# 1. rad | 2. ptratio

# Cada una contiene 40 NA.

#Nota: No suele ser normal que dos variables tengan la misma cantidad de NA
# se puede pensar algun tipo de correlacion entre las variables 


```

```{r}
# Existen varias formas de eliminar los na

#1 eliminar todas las observaciones que contengan algún NA
#Nota: Es una forma muy "Barbara", ya puede haber algún tipo de sesgo en los datos (Ej: Votaciones politicas, los NA no se eliminan, se reacomodan)

housing.data.1 <- na.omit(housing.data)

# La funcion na.omit omite los na de las columnas, pero no los elimina. OJO

# hacemos otros sumary para mirar como nos queda la información

summary(housing.data.1)

# en el anterior sumary podemos ver que los na han sido iliminados 


```

```{r}
#2 opción
# Eliminar los NAs de ciertas columnas 

#Nota: es una solución menos bestia
# Por ejemplo eliminar todos menos los de rad


drop_na <- c("rad") # creo el vector con las columnas que no le quiero eliminar los NA

# creo un nuevo dataframe 
housing.data.2 <- housing.data[ 
  complete.cases(housing.data[,!(names(housing.data))%in% drop_na]),]

l = complete.cases(housing.data[, !(names(housing.data)) %in% drop_na])
# la función complete.cases returna un vector de booleanos que indica los casos a los que tiene que completar 

# el codigo anterior omite los NAs de todas las columnas menos las de rad


#lo visualizamos con la funcion summary
summary(housing.data.2)

# Del resumen anterior podemos ver que solo quedaron 35 NAs de rad.


```

```{r}
# Opción 3
# Eliminar toda una columna
# Esto se haria si una columna tiene muchos Nas y  o es de mucha importacia para la investigacion 


# por ejemplo, eliminanos la columna rad


housing.data$rad <- NULL

#El codigo anterior anula toda una columna 

# Podemos verificar haciendo un resumen
summary(housing.data)

# para eliminar mas de una columna seria asi:

drops <- c("rad", "ptratio")# Vector de columnas de las variables a las que les voy a eliminar NAs
housing.data.3 <- housing.data[,!(names(housing.data) %in% drops)]
```

```{r}
# FORMA MENOS AGRESIVA 

# Una opcion no tan barbara de lidiar con NAs es remplazarla por algún estadistidico o valor aleatorio 

# usamos una libreria que contiene una funcion muy util

#install.packages("Hmisc") "<- instale de ser necesario"
library(Hmisc)

```


```{r}
#  creo una copia de la base de datos
housing.data.copy1 <- housing.data
# cambio los na por el promedio de las variables 
housing.data.copy1$ptratio <- impute(housing.data.copy1$ptratio, mean)
housing.data.copy1$rad <- impute(housing.data.copy1$rad, mean)


summary(housing.data.copy1)
```

```{r}

# cambio los na por el promedio de las variables 
housing.data.copy2 <- housing.data

housing.data.copy2$ptratio <- impute(housing.data.copy2$ptratio, median)
housing.data.copy2$rad <- impute(housing.data.copy2$rad, median)
summary(housing.data.copy2)
```

```{r}
# usar un valor predifinido es otra opcion:
housing.data.copy2 <- housing.data
housing.data.copy2$ptratio <- impute(housing.data.copy2$ptratio, 18)
housing.data.copy2$rad <- impute(housing.data.copy2$rad, 7)
summary(housing.data.copy2)
```


# la función impute ayuda mucho para remplazar los na sin tener que acceder a las posiciones


```{r}

#install.packages("mice")
library(mice)
# Sirve para ver el patron que siguen la información que falta 

png(filename = "PatronNA.png")

j = md.pattern(housing.data)

dev.off()
# de lo anterior podemos ver que 431 valores lo tienen todo, 35 donde falta rad pero no ptratio, 35 viceversa y 5 valores donde falta rad y ptratio 


```


```{r}
# Otra forma es visualizar la informacion 
# Se puede hacer con una librearia VIM

#install.packages("VIM")
library(VIM)

png(filename = "FormaAlternativa.png")

aggr(housing.data,
     col= c('green', 'red'),
     numbers = TRUE, 
     sortVars = TRUE,
     labels = names(housing.data),
     cex.axis = 0.75,
     gap = 1,
     ylab = c("Histograma de NAs", "Patrón")
) 

dev.off()
```