# Laboratorio 2 -- Mongodb 🍃

## Restaurar backup
  - [airbnb](https://drive.google.com/drive/folders/1gAtZZdrBKiKioJSZwnShXskaKk6H_gCJ?usp=sharing)

## General

En este base de datos puedes encontrar un montón de apartementos y sus reviews, esto está sacado de hacer webscrapping.

Pregunta Si montarás un sitio real, ¿Qué posible problemas pontenciales les ves a como está almacenada la información?

### Respuesta

El principal problema que puedo detectar es el array de review, por que parece que guardan todas las review dentro del documento de cada apartamento/hotel/habitación. Es posible que ese  array crezca demasiado y que eso comprometa el rendimiento de mongo.

creo que lo idóneo seria tener en ese documento por un lado las ultimas review digamos un array con 10 review y otro array con las 10 mejores valoraciones.

Ademas añadiría otra colección en la que solo tengamos review pero seria documentos mucho mas pequeños y manejables, donde ademas de tener la valoración del cliente tendríamos un campo que haga referencia al sitio.
## Consultas
### Básico

1. Saca en una consulta cuantos apartamentos hay en España.

```javascript

db.listingsAndReviews.countDocuments({
    "address.country":"Spain"
})

let respuesta = 633

```

2. Lista los 10 primeros:
        Sólo muestra: nombre, camas, precio, government_area
        Ordenados por precio.

```javascript
db.listingsAndReviews
        .find(
        {},
        {
                name:1, 
                beds:1, 
                price:1, 
                "addres.government_area":1, 
                _id:0
        }
        )
        .sort({price:1})
        .limit(10)
```

### Filtrando

1. Queremos viajar comodos, somos 4 personas y queremos:
        4 camas.
        Dos cuartos de baño.

```javascript
db.listingsAndReviews.find(
    {
        beds:{$gte:4},
        bathrooms:{$gte:2}
    }
)
```

2. Al requisito anterior,hay que añadir que nos gusta la tecnología queremos que el apartamento tenga wifi.

```javascript
db.listingsAndReviews.find(
    {
        beds:{$gte:4},
        bathrooms:{$gte:2},
        amenities:{
            $all:['Wifi']
        }
        
    }
)
```

3. Y bueno, un amigo se ha unido que trae un perro, así que a la query anterior tenemos que buscar que permitan mascota Pets Allowed

```javascript

// añado una cama mas no queremos que el amigo duerma con el perro
db.listingsAndReviews.find(
    {
        beds:{$gte:5},
        bathrooms:{$gte:2},
        amenities:{
            $all:['Wifi', 'Pets allowed']
        },
        
    }
).size()
```

### Operadores lógicos

1. Estamos entre ir a Barcelona o a Portugal, los dos destinos nos valen, peeero... queremos que el precio nos salga baratito (50 $), y que tenga buen rating de reviews

```javascript
db.listingsAndReviews.find(
    {
        $or:[
            {"address.country":"Portugal"},
            {"address.market":"Barcelona"}
        ],
        $and:[
            {price:{$lte:50}},
            {"review_scores.review_scores_rating":{$gte:90}}
        ]
    },
)
```

## Agregaciones
### Basico

1. Queremos mostrar los pisos que hay en España, y los siguiente campos:
        Nombre.
        De que ciudad (no queremos mostrar un objeto, sólo el string con la ciudad)
        El precio (no queremos mostrar un objeto, sólo el campo de precio)

```javascript
db.listingsAndReviews.aggregate([
    {
        $match:{
            "address.country":"Spain",
        }
    },
    {
        $project: {
            nombre: "$name",
            ciudad: "$address.market",
            precio:{$toString: "$price"},
            _id:0
        }
    }
])
```

2. Queremos saber cuantos alojamientos hay disponibles por pais.

```javascript
db.listingsAndReviews.aggregate([
    {
        $project:{
            "address.country":1
        }
    },
    {
        $group: {
          _id: "$address.country",
          alojamientos_disponibles: {
            $sum: 1
          }
        }
    }
])
```

## Opcional

1. Queremos saber el precio medio de alquiler de airbnb en España.

```javascript
//TODO pegar consulta
```

2. ¿Y si quisieramos hacer como el anterior, pero sacarlo por paises?

```javascript
//TODO pegar consulta
```

3. Repite los mismos pasos pero agrupando también por numero de habitaciones.

```javascript
//TODO pegar consulta
```

## Desafío

1. Queremos mostrar el top 5 de apartamentos más caros en España, y sacar los siguentes campos:

    Nombre.
    Ciudad.
    Amenities, pero en vez de un array, un string con todos los ammenities.

```javascript
//TODO pegar consulta
```
