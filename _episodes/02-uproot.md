---
title: "Operaciones básicas de entrada/salida de archivos con Uproot"
teaching: 25
exercises: 0
questions:
 - "¿Cómo puedo encontrar mis datos en un archivo ROOT?"
 - "¿Cómo puedo graficarlos?"
 - "¿Cómo puedo escribir nuevos datos en un archivo ROOT?"
objectives:
 - "Aprender a navegar por un archivo ROOT."
 - "Aprender cómo enviar los datos ROOT a las librerías que pueden actuar sobre ellos."
 - "Aprender a escribir histogramas y TTrees en archivos."
keypoints:
 - "Los TDirectories y TTrees de Uproot tienen una interfaz similar a la de un diccionario."
 - "Los métodos de lectura de Uproot están principalmente destinados a enviar datos a una librería más especializada."
 - "La escritura con Uproot es más limitada, pero puede escribir histogramas y TTrees."
---

# ¿Qué es Uproot?

Uproot es un paquete de Python que lee y escribe archivos ROOT, y está *únicamente* enfocado en la lectura y escritura (sin análisis, sin gráficos, etc.). Interactúa con NumPy, Awkward Array y Pandas para cálculos, boost-histogram/hist para manipulación y visualización de histogramas, Vector para funciones y transformaciones de vectores de Lorentz, Coffea para escalado, etc.

Uproot está implementado solo con Python y librerías de Python. No tiene una parte compilada ni requiere una versión específica de ROOT. (Esto significa que si *usas* ROOT para algo más que entrada/salida, tu elección de versión de ROOT no estará limitada por la entrada/salida).

![abstraction-layers]({{ page.root }}/fig/abstraction-layers.png)

Como consecuencia de ser una implementación independiente de entrata/salida de ROOT, Uproot podría no ser capaz de leer/escribir ciertos tipos de datos. Qué tipos de datos no están implementados es un objetivo en constante movimiento, ya que siempre se están agregando nuevos. Una buena forma de leer datos es simplemente intentarlo y ver si Uproot genera algún error. Para la escritura, consulta las listas de tipos compatibles en la [documentación de Uproot](https://uproot.readthedocs.io/en/latest/basic.html#writing-objects-to-a-file) (cajas azules en el texto).

# Leer datos desde un archivo

## Abrir el archivo

Para abrir un archivo para lectura, pasa el nombre del archivo a [uproot.open](https://uproot.readthedocs.io/en/latest/uproot.reading.open.html). En los scripts, es una buena práctica usar la [instrucción with de Python](https://realpython.com/python-with-statement/) para cerrar el archivo cuando termines, pero si estás trabajando de forma interactiva, puedes usar una asignación directa.

```python
import skhep_testdata

nombre_del_archivo = skhep_testdata.data_path(
    "uproot-Event.root"
)  # descarga este archivo de prueba y obtiene una ruta local hacia él

import uproot

archivo = uproot.open(nombre_del_archivo)
```

Para acceder a un archivo remoto mediante HTTP o XRootD, utiliza una URL que comience con `"http://..."`, `"https://..."`, o `"root://..."`. Si la interfaz de Python para XRootD no está instalada, el mensaje de error explicará cómo instalarla.

## Listar contenidos

Este objeto "`archivo`" en realidad representa un directorio, y los objetos nombrados en ese directorio son accesibles a través de una interfaz similar a un diccionario. Por lo tanto, `keys`, `values`, y `items` devuelven los nombres de las claves y/o leen los datos. Si solo quieres listar los objetos sin leerlos, utiliza `keys`. (Esto es similar a `ls()` de ROOT, excepto que obtienes una lista en Python).

```python
archivo.keys()
```

A menudo, también querrás conocer el tipo de cada objeto, por lo que los objetos [uproot.ReadOnlyDirectory](https://uproot.readthedocs.io/en/latest/uproot.reading.ReadOnlyDirectory.html) también tienen un método `classnames`, que devuelve un diccionario de nombres de objetos a nombres de clases (sin leerlos).

```python
archivo.classnames()
```

## Lectura de un histograma

Si estás familiarizado con ROOT, `TH1F` te resultará reconocible como histogramas y `TTree` como un conjunto de datos. Para leer uno de los histogramas, coloca su nombre entre corchetes:

```python
h = archivo["hstat"]
h
```

Uproot no realiza ningún tipo de graficación ni manipulación de histogramas, por lo que los métodos más útiles de `h` comienzan con "to": `to_boost` (boost-histogram), `to_hist` (hist), `to_numpy` (la tupla de 2 elementos de NumPy que contiene el contenido y los bordes), `to_pyroot` (PyROOT), etc.

```python
h.to_hist().plot()
```

Los histogramas de Uproot también cumplen con el [protocolo de graficación UHI](https://uhi.readthedocs.io/en/latest/plotting.html), por lo que tienen métodos como `values` (contenidos de los bins), `variances` (errores al cuadrado) y `axes`.

```python
h.values()
h.variances()
list(h.axes[0])  # "x", "y", "z" o 0, 1, 2
```

## Lectura de un TTree

Un TTree representa un conjunto de datos potencialmente grande. Obtenerlo del [uproot.ReadOnlyDirectory](https://uproot.readthedocs.io/en/latest/uproot.reading.ReadOnlyDirectory.html) solo devuelve los nombres y tipos de sus TBranch. El método `show` es una forma conveniente de listar su contenido:

```python
t = archivo["T"]
t.show()
```

Ten en cuenta que puedes obtener la misma información de `keys` (un [uproot.TTree](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TTree.TTree.html) es similar a un diccionario), `typename` e `interpretation`.

```python
t.keys()
t["event/fNtrack"]
t["event/fNtrack"].typename
t["event/fNtrack"].interpretation
```

(Si un [uproot.TBranch](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TBranch.TBranch.html) no tiene `interpretation`, no se puede leer con Uproot.)

La forma más directa de leer datos de un [uproot.TBranch](https://uproot.readthedocs.io/en/latest/uproot.behaviors.TBranch.TBranch.html) es llamando a su método `array`.

```python
t["event/fNtrack"].array()
```

Consideraremos otros métodos en la próxima lección.

## Leyendo un... ¿qué es eso?

Este archivo también contiene una instancia del tipo [TProcessID](https://root.cern.ch/doc/master/classTProcessID.html). Estos tipos de objetos no son típicamente útiles en el análisis de datos, pero Uproot logra leerlo de todos modos porque sigue ciertas convenciones (tiene "streamers de clase"). Se presenta como un objeto genérico con una propiedad `all_members` para sus miembros de datos (a través de todas las superclases).

```python
archivo["ProcessID0"]
archivo["ProcessID0"].all_members
```

Aquí hay un ejemplo más útil de eso: una búsqueda de supernovas con el experimento IceCube tiene clases personalizadas para sus datos, que Uproot lee y representa como objetos con `all_members`.

```python
icecube = uproot.open(skhep_testdata.data_path("uproot-issue283.root"))
icecube.classnames()

icecube["config/detector"].all_members
icecube["config/detector"].all_members["ChannelIDMap"]
```

# Escribiendo datos en un archivo

La capacidad de Uproot para *escribir* datos es más limitada que su capacidad para *leer* datos, pero algunos casos útiles son posibles.

## Abrir archivos para escribir

Primero que nada, un archivo debe ser abierto para escribir, ya sea creando un archivo completamente nuevo o actualizando uno existente.

```python
archivo1 = uproot.recreate("archivo-completamente-nuevo.root")
archivo2 = uproot.update("archivo-existente.root")
```

(Uproot no puede escribir a través de una red; los archivos de salida deben ser locales.)

## Escribiendo cadenas y histogramas

Estos objetos [uproot.WritableDirectory](https://uproot.readthedocs.io/en/latest/uproot.writing.writable.WritableDirectory.html) tienen una interfaz similar a un diccionario: puedes poner datos en ellos asignando a corchetes.

```python
archivo1["una_cadena"] = "Este objeto va a ser un TObjString."

output1["un_histograma"] = archivo["hstat"]

import numpy as np

output1["un_directorio/otro_histograma"] = np.histogram(
    np.random.normal(0, 1, 1000000)
)
```

En ROOT, el nombre de un objeto es una propiedad del objeto, pero en Uproot, es una clave en el TDirectory que contiene el objeto, por lo que el nombre está en el lado izquierdo de la asignación, entre corchetes. Solo se admiten los tipos de datos enumerados en el cuadro azul [en la documentación](https://uproot.readthedocs.io/en/latest/basic.html#writing-objects-to-a-file): principalmente solo histogramas.

## Escribiendo TTrees

Los TTrees son potencialmente grandes y pueden no caber en la memoria. Generalmente, necesitarás escribirlos en lotes.

Una forma de hacer esto es asignar el primer lote y `extend` con lotes posteriores:

```python
import numpy as np

archivo1["tree1"] = {
    "x": np.random.randint(0, 10, 1000000),
    "y": np.random.normal(0, 1, 1000000),
}
archivo1["tree1"].extend(
    {"x": np.random.randint(0, 10, 1000000), "y": np.random.normal(0, 1, 1000000)}
)
archivo1["tree1"].extend(
    {"x": np.random.randint(0, 10, 1000000), "y": np.random.normal(0, 1, 1000000)}
)
```

Otra forma es crear un TTree vacío con [uproot.WritableDirectory.mktree](https://uproot.readthedocs.io/en/latest/uproot.writing.writable.WritableDirectory.html#mktree), de modo que cada escritura sea una extensión.

```python
archivo1.mktree("tree2", {"x": np.int32, "y": np.float64})
archivo1["tree2"].extend(
    {"x": np.random.randint(0, 10, 1000000), "y": np.random.normal(0, 1, 1000000)}
)
archivo1["tree2"].extend(
    {"x": np.random.randint(0, 10, 1000000), "y": np.random.normal(0, 1, 1000000)}
)
archivo1["tree2"].extend(
    {"x": np.random.randint(0, 10, 1000000), "y": np.random.normal(0, 1, 1000000)}
)
```

Se dan consejos de rendimiento en la próxima lección, pero en general, es más beneficioso escribir pocos lotes grandes en lugar de muchos lotes pequeños.

Los únicos tipos de datos que se pueden asignar o pasar a `extend` están listados en la caja azul [en esta documentación](https://uproot.readthedocs.io/en/latest/basic.html#writing-ttrees-to-a-file). Esto incluye arrays dentados (descritos en la lección después de la próxima), pero no tipos más complejos.

{% include links.md %}
