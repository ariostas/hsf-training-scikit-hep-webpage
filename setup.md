---
title: Setup
---

# Para el tutorial

Este tutorial utiliza una muestra de los paquetes del [proyecto Scikit-HEP](https://scikit-hep.org/) (Uproot, Awkward Array, hist, Vector, zfit, Particle, fastjet), que son todos componentes que podrías utilizar en tu análisis, así como Python 3, NumPy y una variedad de otras librerías populares (Pandas, Matplotlib, JupyterLab, Numba).

En lugar de pedirte que los instales todos, ofrecemos dos formas diferentes de ejecutar todo en tu navegador: GitHub Codespaces y Binder.
Recomendamos que uses GitHub Codespaces (lee las instrucciones a continuación). Si esta opción no es viable para ti, puedes usar Binder, aunque ten en cuenta que los recursos podrían ser muy limitados.

## GitHub Codespaces

<!--
<p align="center">
  <iframe width="427" height="251" src="https://www.youtube.com/embed/gcAuyqW4QRc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>
-->

Para usar GitHub Codespaces, necesitas crear una cuenta en [GitHub](https://github.com/) (¡es gratis! También puedes unirte a su programa educativo para obtener más beneficios gratuitos).

Haz clic en el siguiente botón (se abrirá una nueva página), luego vuelve a estas instrucciones:

<p align="center">
  <a href="https://codespaces.new/hsf-training/hsf-training-scikit-hep-webpage?quickstart=1" target="_blank">
    <img src="https://github.com/codespaces/badge.svg" alt="Iniciar GitHub Codespaces">
  </a>
</p>

Después de iniciar el codespace de GitHub, observa las líneas en la pestaña "Terminal" a continuación. Puede tomar un tiempo hasta que el entorno se configure por completo. Si ves líneas como estas:

```
Use Cmd/Ctrl + Shift + P -> View Creation Log to see full logs
✔ Finishing up...
✔ Running updateContentCommand...
⠦ Running postCreateCommand...
  › python3 -m pip install -r requirements.txt
```

la instalación sigue en progreso.

Una vez que hayas terminado, deberías ver solo un prompt vacío como este:

```
@klieret ➜ /workspaces/hsf-training-scikit-hep-webpage (main) $
```

Espera unos segundos para asegurarte de que no se ejecute nada más.

Ahora mira el panel lateral izquierdo. Deberías ver un archivo llamado `notebook.ipynb`. Haz clic en él.
Se abrirá una nueva pestaña con el notebook.

> ## Nota
> Si el notebook no aparece después de varios minutos (y solo ves la "barra de progreso azul" en la parte superior),
> podrías intentar usar un navegador diferente. Ha habido informes de que esto ocurre con
> Safari en macOS y con ventanas privadas de Firefox.
{: .callout}

Intenta ejecutar la primera línea para verificar que todo esté configurado correctamente.

¡Estás listo para comenzar 🎉!

## Binder

Simplemente haz clic en el siguiente botón:

<p align="center">
  <a href="https://mybinder.org/v2/gh/hsf-training/hsf-training-scikit-hep-webpage/main?urlpath=lab" target="_blank">
    <img src="https://mybinder.org/badge_logo.svg" alt="Iniciar Binder">
  </a>
</p>

# Después del tutorial

Si deseas instalar algunos de estos paquetes en tu propio computador o en el de tu laboratorio, te recomiendo [Miniforge](https://github.com/conda-forge/miniforge) (o Anaconda/Miniconda con el canal [conda-forge](https://conda-forge.org/docs/user/introduction.html#how-can-i-install-packages-from-conda-forge)). Este método también proporciona una manera de [instalar ROOT en el mismo entorno](https://github.com/conda-forge/root-feedstock#readme). Para configurar el entorno utiliza el archivo [environment.yml](https://github.com/hsf-training/hsf-training-scikit-hep-webpage/blob/main/environment.yml) (junto con su archivo [requirements.txt](https://github.com/hsf-training/hsf-training-scikit-hep-webpage/blob/main/requirements.txt)) en la [base](https://github.com/hsf-training/hsf-training-scikit-hep-webpage) de este repositorio, como:

```bash
conda install uproot awkward   # ... ¿otros?
```

Alternativamente, puedes instalar todos los paquetes requeridos localmente con pip: consulta el archivo environment.yml y el archivo requirements.txt para una lista (esto incluye todas las dependencias bajo la clave pip y algunas de las dependencias mencionadas anteriormente).


{% include links.md %}
