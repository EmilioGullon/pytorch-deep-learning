[Ver código fuente](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/05_pytorch_going_modular.md) | [Ver diapositivas](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/slides/05_pytorch_going_modular.pdf)

# 05. PyTorch modularizado

Esta sección responde a la pregunta: "¿cómo convierto mi código de notebook en scripts de Python?"

Para hacerlo, vamos a convertir las celdas de código más útiles del [notebook 04. PyTorch Custom Datasets](https://www.learnpytorch.io/04_pytorch_custom_datasets/) en una serie de scripts de Python guardados en un directorio llamado [`going_modular`](https://github.com/mrdbourke/pytorch-deep-learning/tree/main/going_modular).

## ¿Qué significa modularizar?

Modularizar implica convertir código de notebook (de un notebook de Jupyter o Google Colab) en una serie de scripts de Python distintos que ofrezcan una funcionalidad similar.

Por ejemplo, podríamos convertir nuestro código de notebook (celdas) en los siguientes archivos de Python:

- `data_setup.py` - archivo para preparar y descargar datos si hace falta.
- `engine.py` - archivo que contiene varias funciones de entrenamiento.
- `model_builder.py` o `model.py` - archivo para crear un modelo de PyTorch.
- `train.py` - archivo que usa todos los demás para entrenar un modelo objetivo de PyTorch.
- `utils.py` - archivo dedicado a funciones auxiliares útiles.

> **Nota:** El nombre y la estructura de los archivos anteriores dependerán de tu caso de uso y de los requisitos de tu código. Los scripts de Python son tan generales como las celdas individuales de un notebook, es decir, podrías crear uno para casi cualquier funcionalidad.

## ¿Por qué querrías modularizar?

Los notebooks son fantásticos para explorar de forma iterativa y ejecutar experimentos rápidamente.

Sin embargo, en proyectos más grandes, puede que encuentres los scripts de Python más reproducibles y fáciles de ejecutar.

Aunque esto se debate bastante, ya que empresas como [Netflix han mostrado cómo usan notebooks para código en producción](https://netflixtechblog.com/notebook-innovation-591ee3221233).

El **código en producción** es el código que se ejecuta para ofrecer un servicio a alguien o algo.

Por ejemplo, si tienes una aplicación en línea que otras personas pueden usar, el código que ejecuta esa aplicación se considera **código en producción**.

Y librerías como [`nb-dev`](https://github.com/fastai/nbdev) de fast.ai (abreviatura de notebook development) te permiten escribir librerías enteras de Python (incluida documentación) con notebooks de Jupyter.

### Ventajas y desventajas: notebooks vs scripts de Python

Hay argumentos para ambos lados.

Pero esta lista resume algunos de los temas principales.

|               | **Pros**                                               | **Contras**                                    |
| ------------- | ------------------------------------------------------ | ---------------------------------------------- |
| **Notebooks** | Fácil experimentar/empezar                             | El versionado puede ser difícil                |
|               | Fácil de compartir (por ejemplo, un enlace a Colab)    | Difícil usar solo partes específicas           |
|               | Muy visual                                              | Texto y gráficos pueden estorbar al código     |

|                    | **Pros**                                                                            | **Contras**                                                                                     |
| ------------------ | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Scripts Python** | Puedes empaquetar código (evitas reescribir lógica entre notebooks)                | Experimentar no es tan visual (normalmente ejecutas todo el script, no una sola celda)         |
|                    | Puedes usar git para versionado                                                     |                                                                                                  |
|                    | Muchos proyectos open source usan scripts                                           |                                                                                                  |
|                    | Proyectos grandes pueden ejecutarse en la nube (menos soporte para notebooks)      |                                                                                                  |

### Mi flujo de trabajo

Normalmente empiezo proyectos de machine learning en notebooks de Jupyter/Google Colab para experimentar y visualizar rápido.

Luego, cuando ya tengo algo funcionando, muevo las partes de código más útiles a scripts de Python.

<img src="https://raw.githubusercontent.com/mrdbourke/pytorch-deep-learning/main/images/05-my-workflow-for-experimenting.png" alt="flujo de trabajo posible para escribir código de machine learning: empezar con jupyter o google colab y luego pasar a scripts de python cuando ya funciona" width=1000/>

*Hay muchos flujos de trabajo posibles para escribir código de machine learning. Algunas personas prefieren empezar con scripts; otras (como yo) prefieren empezar con notebooks y luego pasar a scripts.*

### PyTorch en la práctica real

En proyectos reales, verás que muchos repositorios de ML con PyTorch tienen instrucciones para ejecutar el código en forma de scripts de Python.

Por ejemplo, te pueden indicar ejecutar algo como esto en terminal/línea de comandos para entrenar un modelo:

```
python train.py --model MODEL_NAME --batch_size BATCH_SIZE --lr LEARNING_RATE --num_epochs NUM_EPOCHS
```

<img src="https://raw.githubusercontent.com/mrdbourke/pytorch-deep-learning/main/images/05-python-train-command-line-annotated.png" alt="comando de terminal para entrenar un script de pytorch con diferentes hiperparámetros" width=1000/>

*Ejecutar un script `train.py` de PyTorch por línea de comandos con diferentes configuraciones de hiperparámetros.*

En este caso, `train.py` es el script objetivo; probablemente contiene funciones para entrenar un modelo de PyTorch.

Y `--model`, `--batch_size`, `--lr` y `--num_epochs` se conocen como flags de argumentos.

Puedes configurarlos con los valores que quieras y, si son compatibles con `train.py`, funcionarán; si no, lanzarán error.

Por ejemplo, si quisiéramos entrenar nuestro modelo TinyVGG del notebook 04 durante 10 épocas, con batch size 32 y learning rate 0.001:

```
python train.py --model tinyvgg --batch_size 32 --lr 0.001 --num_epochs 10
```

Puedes crear tantos flags de argumentos como necesites en tu `train.py`.

La receta del blog de PyTorch para entrenar modelos de visión por computador de última generación sigue este estilo.

<img src="https://raw.githubusercontent.com/mrdbourke/pytorch-deep-learning/main/images/05-training-sota-recipe.png" alt="receta de script de entrenamiento de pytorch para modelos de visión por computador de última generación" width=800/>

*Receta de script de entrenamiento de PyTorch para entrenar modelos SOTA de visión por computador con 8 GPUs. Fuente: [blog de PyTorch](https://pytorch.org/blog/how-to-train-state-of-the-art-models-using-torchvision-latest-primitives/#the-training-recipe).* 

## Qué vamos a cubrir

El concepto principal de esta sección es: **convertir celdas de notebook útiles en archivos reutilizables de Python.**

Hacer esto nos ahorrará escribir el mismo código una y otra vez.

Hay dos notebooks para esta sección:

1. [**05. Going Modular: Parte 1 (modo celdas)**](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/going_modular/05_pytorch_going_modular_cell_mode.ipynb) - este notebook se ejecuta como un notebook tradicional de Jupyter/Colab y es una versión condensada del [notebook 04](https://www.learnpytorch.io/04_pytorch_custom_datasets/).
2. [**05. Going Modular: Parte 2 (modo script)**](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/going_modular/05_pytorch_going_modular_script_mode.ipynb) - este notebook es igual al anterior pero con funcionalidad añadida para convertir cada sección principal en scripts de Python, como `data_setup.py` y `train.py`.

El texto de este documento se centra en las celdas de código de la Parte 2 (modo script), las que tienen `%%writefile ...` al principio.

### ¿Por qué dos partes?

Porque a veces la mejor forma de aprender algo es ver cómo *difiere* de otra cosa.

Si ejecutas ambos notebooks en paralelo verás cómo difieren, y ahí están los aprendizajes clave.

<img src="https://raw.githubusercontent.com/mrdbourke/pytorch-deep-learning/main/images/05-notebook-cell-mode-vs-script-mode.png" alt="ejecución lado a lado de notebook en modo celdas vs modo script" width=1000/>

*Si ejecutas los dos notebooks de la sección 05 lado a lado, notarás que el notebook en **modo script tiene celdas extra** para convertir código en scripts de Python.*

### Objetivo final

Al final de esta sección queremos tener dos cosas:

1. Poder entrenar el modelo que construimos en el notebook 04 (Food Vision Mini) con una sola línea en terminal: `python train.py`.
2. Una estructura de directorios con scripts reutilizables de Python, como:

```
going_modular/
├── going_modular/
│   ├── data_setup.py
│   ├── engine.py
│   ├── model_builder.py
│   ├── train.py
│   └── utils.py
├── models/
│   ├── 05_going_modular_cell_mode_tinyvgg_model.pth
│   └── 05_going_modular_script_mode_tinyvgg_model.pth
└── data/
    └── pizza_steak_sushi/
        ├── train/
        │   ├── pizza/
        │   │   ├── image01.jpeg
        │   │   └── ...
        │   ├── steak/
        │   └── sushi/
        └── test/
            ├── pizza/
            ├── steak/
            └── sushi/
```

### Cosas a tener en cuenta

- **Docstrings** - Es importante escribir código reproducible y comprensible. Con eso en mente, cada función/clase que pasaremos a scripts está creada siguiendo el estilo de docstrings de Google [Python docstring style](https://google.github.io/styleguide/pyguide.html#383-functions-and-methods).
- **Imports al principio de los scripts** - Como todos los scripts de Python que vamos a crear pueden considerarse pequeños programas por sí mismos, necesitan importar sus módulos al inicio, por ejemplo:

```python
# Import modules required for train.py
import os
import torch
import data_setup, engine, model_builder, utils

from torchvision import transforms
```

## ¿Dónde puedes pedir ayuda?

Todos los materiales del curso [están disponibles en GitHub](https://github.com/mrdbourke/pytorch-deep-learning).

Si tienes problemas, puedes preguntar en [GitHub Discussions del curso](https://github.com/mrdbourke/pytorch-deep-learning/discussions).

Y por supuesto, tienes la [documentación de PyTorch](https://pytorch.org/docs/stable/index.html) y los [foros de desarrolladores de PyTorch](https://discuss.pytorch.org/), un sitio muy útil para todo lo relacionado con PyTorch.

## 0. Modo celdas vs modo script

Un notebook en modo celdas, como [05. Going Modular Parte 1 (modo celdas)](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/going_modular/05_pytorch_going_modular_cell_mode.ipynb), se ejecuta de forma normal: cada celda del notebook es código o markdown.

Un notebook en modo script, como [05. Going Modular Parte 2 (modo script)](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/going_modular/05_pytorch_going_modular_script_mode.ipynb), es muy similar; sin embargo, muchas celdas de código pueden convertirse en scripts de Python.

> **Nota:** No *necesitas* crear scripts de Python desde un notebook; también puedes crearlos directamente en un IDE como [VS Code](https://code.visualstudio.com/). Tener el notebook en modo script en esta sección es solo para demostrar una forma de pasar de notebooks a scripts de Python.

## 1. Obtener datos

Obtener los datos en ambos notebooks de la sección 05 ocurre igual que en el [notebook 04](https://www.learnpytorch.io/04_pytorch_custom_datasets/#1-get-data).

Se hace una llamada a GitHub usando el módulo `requests` de Python para descargar un `.zip` y descomprimirlo.

```python
import os
import requests
import zipfile
from pathlib import Path

# Setup path to data folder
data_path = Path("data/")
image_path = data_path / "pizza_steak_sushi"

# If the image folder doesn't exist, download it and prepare it... 
if image_path.is_dir():
    print(f"{image_path} directory exists.")
else:
    print(f"Did not find {image_path} directory, creating one...")
    image_path.mkdir(parents=True, exist_ok=True)
    
# Download pizza, steak, sushi data
with open(data_path / "pizza_steak_sushi.zip", "wb") as f:
    request = requests.get("https://github.com/mrdbourke/pytorch-deep-learning/raw/main/data/pizza_steak_sushi.zip")
    print("Downloading pizza, steak, sushi data...")
    f.write(request.content)

# Unzip pizza, steak, sushi data
with zipfile.ZipFile(data_path / "pizza_steak_sushi.zip", "r") as zip_ref:
    print("Unzipping pizza, steak, sushi data...") 
    zip_ref.extractall(image_path)

# Remove zip file
os.remove(data_path / "pizza_steak_sushi.zip")
```

Esto deja un directorio `data` que contiene otro directorio llamado `pizza_steak_sushi` con imágenes de pizza, steak y sushi en formato estándar de clasificación de imágenes.

```
data/
└── pizza_steak_sushi/
    ├── train/
    │   ├── pizza/
    │   │   ├── train_image01.jpeg
    │   │   ├── test_image02.jpeg
    │   │   └── ...
    │   ├── steak/
    │   │   └── ...
    │   └── sushi/
    │       └── ...
    └── test/
        ├── pizza/
        │   ├── test_image01.jpeg
        │   └── test_image02.jpeg
        ├── steak/
        └── sushi/
```

## 2. Crear Datasets y DataLoaders (`data_setup.py`)

Una vez tenemos datos, podemos convertirlos en `Dataset` y `DataLoader` de PyTorch (uno para entrenamiento y otro para test).

Convertimos el código útil de creación de `Dataset` y `DataLoader` en una función llamada `create_dataloaders()`.

Y lo escribimos en archivo con `%%writefile going_modular/data_setup.py`.

```py title="data_setup.py"
%%writefile going_modular/data_setup.py
"""
Contains functionality for creating PyTorch DataLoaders for 
image classification data.
"""
import os

from torchvision import datasets, transforms
from torch.utils.data import DataLoader

NUM_WORKERS = os.cpu_count()

def create_dataloaders(
    train_dir: str, 
    test_dir: str, 
    transform: transforms.Compose, 
    batch_size: int, 
    num_workers: int=NUM_WORKERS
):
  """Creates training and testing DataLoaders.

  Takes in a training directory and testing directory path and turns
  them into PyTorch Datasets and then into PyTorch DataLoaders.

  Args:
    train_dir: Path to training directory.
    test_dir: Path to testing directory.
    transform: torchvision transforms to perform on training and testing data.
    batch_size: Number of samples per batch in each of the DataLoaders.
    num_workers: An integer for number of workers per DataLoader.

  Returns:
    A tuple of (train_dataloader, test_dataloader, class_names).
    Where class_names is a list of the target classes.
    Example usage:
      train_dataloader, test_dataloader, class_names = \
        = create_dataloaders(train_dir=path/to/train_dir,
                             test_dir=path/to/test_dir,
                             transform=some_transform,
                             batch_size=32,
                             num_workers=4)
  """
  # Use ImageFolder to create dataset(s)
  train_data = datasets.ImageFolder(train_dir, transform=transform)
  test_data = datasets.ImageFolder(test_dir, transform=transform)

  # Get class names
  class_names = train_data.classes

  # Turn images into data loaders
  train_dataloader = DataLoader(
      train_data,
      batch_size=batch_size,
      shuffle=True,
      num_workers=num_workers,
      pin_memory=True,
  )
  test_dataloader = DataLoader(
      test_data,
      batch_size=batch_size,
      shuffle=False, # don't need to shuffle test data
      num_workers=num_workers,
      pin_memory=True,
  )

  return train_dataloader, test_dataloader, class_names
```

Si queremos crear `DataLoader`, ahora podemos usar la función dentro de `data_setup.py` así:

```python
# Import data_setup.py
from going_modular import data_setup

# Create train/test dataloader and get class names as a list
train_dataloader, test_dataloader, class_names = data_setup.create_dataloaders(...)
```

## 3. Crear un modelo (`model_builder.py`)

En los últimos notebooks (03 y 04), hemos construido el modelo TinyVGG varias veces.

Por eso tiene sentido poner el modelo en su propio archivo para poder reutilizarlo.

Vamos a poner nuestra clase `TinyVGG()` en un script con `%%writefile going_modular/model_builder.py`:

```python title="model_builder.py"
%%writefile going_modular/model_builder.py
"""
Contains PyTorch model code to instantiate a TinyVGG model.
"""
import torch
from torch import nn 

class TinyVGG(nn.Module):
  """Creates the TinyVGG architecture.

  Replicates the TinyVGG architecture from the CNN explainer website in PyTorch.
  See the original architecture here: https://poloclub.github.io/cnn-explainer/
  
  Args:
    input_shape: An integer indicating number of input channels.
    hidden_units: An integer indicating number of hidden units between layers.
    output_shape: An integer indicating number of output units.
  """
  def __init__(self, input_shape: int, hidden_units: int, output_shape: int) -> None:
      super().__init__()
      self.conv_block_1 = nn.Sequential(
          nn.Conv2d(in_channels=input_shape, 
                    out_channels=hidden_units, 
                    kernel_size=3, 
                    stride=1, 
                    padding=0),  
          nn.ReLU(),
          nn.Conv2d(in_channels=hidden_units, 
                    out_channels=hidden_units,
                    kernel_size=3,
                    stride=1,
                    padding=0),
          nn.ReLU(),
          nn.MaxPool2d(kernel_size=2,
                        stride=2)
      )
      self.conv_block_2 = nn.Sequential(
          nn.Conv2d(hidden_units, hidden_units, kernel_size=3, padding=0),
          nn.ReLU(),
          nn.Conv2d(hidden_units, hidden_units, kernel_size=3, padding=0),
          nn.ReLU(),
          nn.MaxPool2d(2)
      )
      self.classifier = nn.Sequential(
          nn.Flatten(),
          # Where did this in_features shape come from? 
          # It's because each layer of our network compresses and changes the shape of our inputs data.
          nn.Linear(in_features=hidden_units*13*13,
                    out_features=output_shape)
      )
    
  def forward(self, x: torch.Tensor):
      x = self.conv_block_1(x)
      x = self.conv_block_2(x)
      x = self.classifier(x)
      return x
      # return self.classifier(self.conv_block_2(self.conv_block_1(x))) # <- leverage the benefits of operator fusion
```

Ahora, en lugar de escribir TinyVGG desde cero cada vez, podemos importarlo así:

```python
import torch
# Import model_builder.py
from going_modular import model_builder
device = "cuda" if torch.cuda.is_available() else "cpu"

# Instantiate an instance of the model from the "model_builder.py" script
torch.manual_seed(42)
model = model_builder.TinyVGG(input_shape=3,
                              hidden_units=10, 
                              output_shape=len(class_names)).to(device)
```

## 4. Crear `train_step()`, `test_step()` y `train()` para combinarlas

Escribimos varias funciones de entrenamiento en el [notebook 04](https://www.learnpytorch.io/04_pytorch_custom_datasets/#75-create-train-test-loop-functions):

1. `train_step()` - recibe un modelo, un `DataLoader`, una función de pérdida y un optimizador, y entrena el modelo sobre ese `DataLoader`.
2. `test_step()` - recibe un modelo, un `DataLoader` y una función de pérdida, y evalúa el modelo sobre ese `DataLoader`.
3. `train()` - ejecuta 1. y 2. durante un número de épocas y devuelve un diccionario de resultados.

Como estas funciones serán el *motor* del entrenamiento, podemos ponerlas en un script llamado `engine.py` con `%%writefile going_modular/engine.py`:

```python title="engine.py"
%%writefile going_modular/engine.py
"""
Contains functions for training and testing a PyTorch model.
"""
import torch

from tqdm.auto import tqdm
from typing import Dict, List, Tuple

def train_step(model: torch.nn.Module, 
               dataloader: torch.utils.data.DataLoader, 
               loss_fn: torch.nn.Module, 
               optimizer: torch.optim.Optimizer,
               device: torch.device) -> Tuple[float, float]:
  """Trains a PyTorch model for a single epoch.

  Turns a target PyTorch model to training mode and then
  runs through all of the required training steps (forward
  pass, loss calculation, optimizer step).

  Args:
    model: A PyTorch model to be trained.
    dataloader: A DataLoader instance for the model to be trained on.
    loss_fn: A PyTorch loss function to minimize.
    optimizer: A PyTorch optimizer to help minimize the loss function.
    device: A target device to compute on (e.g. "cuda" or "cpu").

  Returns:
    A tuple of training loss and training accuracy metrics.
    In the form (train_loss, train_accuracy). For example:
    
    (0.1112, 0.8743)
  """
  # Put model in train mode
  model.train()
  
  # Setup train loss and train accuracy values
  train_loss, train_acc = 0, 0
  
  # Loop through data loader data batches
  for batch, (X, y) in enumerate(dataloader):
      # Send data to target device
      X, y = X.to(device), y.to(device)

      # 1. Forward pass
      y_pred = model(X)

      # 2. Calculate  and accumulate loss
      loss = loss_fn(y_pred, y)
      train_loss += loss.item() 

      # 3. Optimizer zero grad
      optimizer.zero_grad()

      # 4. Loss backward
      loss.backward()

      # 5. Optimizer step
      optimizer.step()

      # Calculate and accumulate accuracy metric across all batches
      y_pred_class = torch.argmax(torch.softmax(y_pred, dim=1), dim=1)
      train_acc += (y_pred_class == y).sum().item()/len(y_pred)

  # Adjust metrics to get average loss and accuracy per batch 
  train_loss = train_loss / len(dataloader)
  train_acc = train_acc / len(dataloader)
  return train_loss, train_acc

def test_step(model: torch.nn.Module, 
              dataloader: torch.utils.data.DataLoader, 
              loss_fn: torch.nn.Module,
              device: torch.device) -> Tuple[float, float]:
  """Tests a PyTorch model for a single epoch.

  Turns a target PyTorch model to "eval" mode and then performs
  a forward pass on a testing dataset.

  Args:
    model: A PyTorch model to be tested.
    dataloader: A DataLoader instance for the model to be tested on.
    loss_fn: A PyTorch loss function to calculate loss on the test data.
    device: A target device to compute on (e.g. "cuda" or "cpu").

  Returns:
    A tuple of testing loss and testing accuracy metrics.
    In the form (test_loss, test_accuracy). For example:
    
    (0.0223, 0.8985)
  """
  # Put model in eval mode
  model.eval() 
  
  # Setup test loss and test accuracy values
  test_loss, test_acc = 0, 0
  
  # Turn on inference context manager
  with torch.inference_mode():
      # Loop through DataLoader batches
      for batch, (X, y) in enumerate(dataloader):
          # Send data to target device
          X, y = X.to(device), y.to(device)
  
          # 1. Forward pass
          test_pred_logits = model(X)

          # 2. Calculate and accumulate loss
          loss = loss_fn(test_pred_logits, y)
          test_loss += loss.item()
          
          # Calculate and accumulate accuracy
          test_pred_labels = test_pred_logits.argmax(dim=1)
          test_acc += ((test_pred_labels == y).sum().item()/len(test_pred_labels))
          
  # Adjust metrics to get average loss and accuracy per batch 
  test_loss = test_loss / len(dataloader)
  test_acc = test_acc / len(dataloader)
  return test_loss, test_acc

def train(model: torch.nn.Module, 
          train_dataloader: torch.utils.data.DataLoader, 
          test_dataloader: torch.utils.data.DataLoader, 
          optimizer: torch.optim.Optimizer,
          loss_fn: torch.nn.Module,
          epochs: int,
          device: torch.device) -> Dict[str, List]:
  """Trains and tests a PyTorch model.

  Passes a target PyTorch models through train_step() and test_step()
  functions for a number of epochs, training and testing the model
  in the same epoch loop.

  Calculates, prints and stores evaluation metrics throughout.

  Args:
    model: A PyTorch model to be trained and tested.
    train_dataloader: A DataLoader instance for the model to be trained on.
    test_dataloader: A DataLoader instance for the model to be tested on.
    optimizer: A PyTorch optimizer to help minimize the loss function.
    loss_fn: A PyTorch loss function to calculate loss on both datasets.
    epochs: An integer indicating how many epochs to train for.
    device: A target device to compute on (e.g. "cuda" or "cpu").

  Returns:
    A dictionary of training and testing loss as well as training and
    testing accuracy metrics. Each metric has a value in a list for 
    each epoch.
    In the form: {train_loss: [...],
                  train_acc: [...],
                  test_loss: [...],
                  test_acc: [...]} 
    For example if training for epochs=2: 
                 {train_loss: [2.0616, 1.0537],
                  train_acc: [0.3945, 0.3945],
                  test_loss: [1.2641, 1.5706],
                  test_acc: [0.3400, 0.2973]} 
  """
  # Create empty results dictionary
  results = {"train_loss": [],
      "train_acc": [],
      "test_loss": [],
      "test_acc": []
  }
  
  # Loop through training and testing steps for a number of epochs
  for epoch in tqdm(range(epochs)):
      train_loss, train_acc = train_step(model=model,
                                          dataloader=train_dataloader,
                                          loss_fn=loss_fn,
                                          optimizer=optimizer,
                                          device=device)
      test_loss, test_acc = test_step(model=model,
          dataloader=test_dataloader,
          loss_fn=loss_fn,
          device=device)
      
      # Print out what's happening
      print(
          f"Epoch: {epoch+1} | "
          f"train_loss: {train_loss:.4f} | "
          f"train_acc: {train_acc:.4f} | "
          f"test_loss: {test_loss:.4f} | "
          f"test_acc: {test_acc:.4f}"
      )

      # Update results dictionary
      results["train_loss"].append(train_loss)
      results["train_acc"].append(train_acc)
      results["test_loss"].append(test_loss)
      results["test_acc"].append(test_acc)

  # Return the filled results at the end of the epochs
  return results
```

Ahora que tenemos `engine.py`, podemos importar funciones así:

```python
# Import engine.py
from going_modular import engine

# Use train() by calling it from engine.py
engine.train(...)
```

## 5. Crear una función para guardar el modelo (`utils.py`)

A menudo querrás guardar un modelo mientras entrena o después de entrenar.

Como ya escribimos ese código varias veces, tiene sentido convertirlo en una función y guardarlo en un archivo.

Es práctica común guardar funciones auxiliares en `utils.py` (de utilities).

Guardemos `save_model()` en `utils.py` con `%%writefile going_modular/utils.py`:

```python title="utils.py"
%%writefile going_modular/utils.py
"""
Contains various utility functions for PyTorch model training and saving.
"""
import torch
from pathlib import Path

def save_model(model: torch.nn.Module,
               target_dir: str,
               model_name: str):
  """Saves a PyTorch model to a target directory.

  Args:
    model: A target PyTorch model to save.
    target_dir: A directory for saving the model to.
    model_name: A filename for the saved model. Should include
      either ".pth" or ".pt" as the file extension.
  
  Example usage:
    save_model(model=model_0,
               target_dir="models",
               model_name="05_going_modular_tingvgg_model.pth")
  """
  # Create target directory
  target_dir_path = Path(target_dir)
  target_dir_path.mkdir(parents=True,
                        exist_ok=True)
  
  # Create model save path
  assert model_name.endswith(".pth") or model_name.endswith(".pt"), "model_name should end with '.pt' or '.pth'"
  model_save_path = target_dir_path / model_name

  # Save the model state_dict()
  print(f"[INFO] Saving model to: {model_save_path}")
  torch.save(obj=model.state_dict(),
             f=model_save_path)
```

Y para usarla:

```python
# Import utils.py
from going_modular import utils

# Save a model to file
save_model(model=...
           target_dir=...,
           model_name=...)
```

## 6. Entrenar, evaluar y guardar el modelo (`train.py`)

Como comentamos antes, en muchos repositorios de PyTorch verás toda la funcionalidad combinada en un `train.py`.

Este archivo básicamente dice: "entrena el modelo con los datos disponibles".

En nuestro `train.py`, combinaremos toda la funcionalidad de los otros scripts para entrenar un modelo.

Así podremos entrenar con una sola línea en terminal:

```
python train.py
```

Para crear `train.py` seguiremos estos pasos:

1. Importar dependencias: `torch`, `os`, `torchvision.transforms` y todos los scripts de `going_modular`: `data_setup`, `engine`, `model_builder`, `utils`.
   - **Nota:** Como `train.py` estará *dentro* de `going_modular`, podemos importar los módulos con `import ...` en vez de `from going_modular import ...`.
2. Configurar hiperparámetros como batch size, número de épocas, learning rate y hidden units (en el futuro se podrían definir con `argparse`).
3. Configurar directorios de entrenamiento y test.
4. Configurar código agnóstico al dispositivo.
5. Crear transformaciones de datos.
6. Crear DataLoaders usando `data_setup.py`.
7. Crear el modelo usando `model_builder.py`.
8. Configurar función de pérdida y optimizador.
9. Entrenar con `engine.py`.
10. Guardar el modelo con `utils.py`.

Y lo creamos desde una celda con `%%writefile going_modular/train.py`:

```python title="train.py"
%%writefile going_modular/train.py
"""
Trains a PyTorch image classification model using device-agnostic code.
"""

import os
import torch
import data_setup, engine, model_builder, utils

from torchvision import transforms

# Setup hyperparameters
NUM_EPOCHS = 5
BATCH_SIZE = 32
HIDDEN_UNITS = 10
LEARNING_RATE = 0.001

# Setup directories
train_dir = "data/pizza_steak_sushi/train"
test_dir = "data/pizza_steak_sushi/test"

# Setup target device
device = "cuda" if torch.cuda.is_available() else "cpu"

# Create transforms
data_transform = transforms.Compose([
  transforms.Resize((64, 64)),
  transforms.ToTensor()
])

# Create DataLoaders with help from data_setup.py
train_dataloader, test_dataloader, class_names = data_setup.create_dataloaders(
    train_dir=train_dir,
    test_dir=test_dir,
    transform=data_transform,
    batch_size=BATCH_SIZE
)

# Create model with help from model_builder.py
model = model_builder.TinyVGG(
    input_shape=3,
    hidden_units=HIDDEN_UNITS,
    output_shape=len(class_names)
).to(device)

# Set loss and optimizer
loss_fn = torch.nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(),
                             lr=LEARNING_RATE)

# Start training with help from engine.py
engine.train(model=model,
             train_dataloader=train_dataloader,
             test_dataloader=test_dataloader,
             loss_fn=loss_fn,
             optimizer=optimizer,
             epochs=NUM_EPOCHS,
             device=device)

# Save the model with help from utils.py
utils.save_model(model=model,
                 target_dir="models",
                 model_name="05_going_modular_script_mode_tinyvgg_model.pth")
```

¡Genial!

Ahora podemos entrenar un modelo de PyTorch con:

```
python train.py
```

Eso aprovechará todos los scripts que hemos creado.

Y si queremos, podemos ajustar `train.py` para usar flags de argumentos con `argparse`, lo cual permitiría probar diferentes hiperparámetros como hablamos antes:

```
python train.py --model MODEL_NAME --batch_size BATCH_SIZE --lr LEARNING_RATE --num_epochs NUM_EPOCHS
```

## Ejercicios

**Recursos:**

- [Notebook plantilla de ejercicios para 05](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/extras/exercises/05_pytorch_going_modular_exercise_template.ipynb)
- [Notebook de soluciones de ejemplo para 05](https://github.com/mrdbourke/pytorch-deep-learning/blob/main/extras/solutions/05_pytorch_going_modular_exercise_solutions.ipynb)
  - Sesión en vivo resolviendo el notebook de soluciones de 05 en YouTube: https://youtu.be/ijgFhMK3pp4

**Ejercicios:**

1. Convierte el código de obtención de datos (sección 1) en un script de Python, por ejemplo `get_data.py`.
   - Al ejecutar `python get_data.py`, debería comprobar si los datos ya existen y saltar la descarga si es así.
   - Si la descarga se completa correctamente, deberías poder acceder a las imágenes `pizza_steak_sushi` desde el directorio `data`.
2. Usa el módulo `argparse` de Python para poder pasar valores personalizados de hiperparámetros a `train.py`.
   - Añade argumentos para cambiar:
     - Directorio de entrenamiento/test
     - Learning rate
     - Batch size
     - Número de épocas
     - Número de hidden units del modelo TinyVGG
   - Mantén los valores por defecto tal como están en el notebook 05.
   - Por ejemplo, deberías poder ejecutar algo como: `python train.py --learning_rate 0.003 --batch_size 64 --num_epochs 20`.
   - **Nota:** Como `train.py` usa otros scripts (`model_builder.py`, `utils.py`, `engine.py`), debes asegurarte de que también estén disponibles. Puedes encontrarlos en la carpeta [`going_modular` del GitHub del curso](https://github.com/mrdbourke/pytorch-deep-learning/tree/main/going_modular/going_modular).
3. Crea un script para predecir (por ejemplo `predict.py`) sobre una imagen objetivo dada una ruta y un modelo guardado.
   - Por ejemplo, deberías poder ejecutar `python predict.py some_image.jpeg` y que un modelo entrenado devuelva su predicción.
   - Para ver código de ejemplo de predicción, revisa la sección de predicción con imagen personalizada del notebook 04: [notebook 04](https://www.learnpytorch.io/04_pytorch_custom_datasets/#113-putting-custom-image-prediction-together-building-a-function).
   - Puede que también tengas que escribir código para cargar un modelo entrenado.

## Extra-curricular

- Para aprender más sobre la estructura de proyectos Python, revisa la guía de Real Python sobre [Python Application Layouts](https://realpython.com/python-application-layouts/).
- Para ideas sobre estilo de código en PyTorch, revisa la [guía de estilo de PyTorch de Igor Susmelj](https://github.com/IgorSusmelj/pytorch-styleguide#recommended-code-structure-for-training-your-model) (gran parte del estilo de este capítulo se basa en ella y en repositorios similares).
- Para ver un ejemplo de `train.py` y otros scripts de entrenamiento escritos por el equipo de PyTorch para clasificación de imágenes SOTA, revisa su repositorio [`classification`](https://github.com/pytorch/vision/tree/main/references/classification).
