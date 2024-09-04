Para enquadrar a câmera automaticamente com base nos dados carregados do JSON, você pode ajustar a posição e o ângulo da câmera dinamicamente no script Python. A ideia é calcular a posição média dos objetos ou o ponto central da cena e ajustar a câmera para enquadrar todos os objetos visíveis.

### 1. Modificação do Script Python

Vamos modificar o script para que, após criar os objetos, a câmera seja posicionada e orientada automaticamente para enquadrar todos eles.

#### Script Python Modificado:

```python
import bpy
import json

# Caminho para o arquivo JSON
filepath = "/caminho/para/o/arquivo/data.json"

# Função para carregar dados JSON
def load_json(filepath):
    with open(filepath, 'r') as file:
        return json.load(file)

# Função para criar objetos a partir dos dados, ajustando altura
def create_objects_from_data(data):
    objects = []
    for item in data:
        # Criar o objeto cubo
        bpy.ops.mesh.primitive_cube_add(size=1, location=(item['x'], item['y'], item['z']))
        obj = bpy.context.object
      
        # Definir nome do objeto
        obj.name = item['name']
      
        # Ajustar altura do cubo (alongar no eixo Z)
        obj.scale.z = item['height']
      
        # Adicionar o objeto à lista
        objects.append(obj)
  
    return objects

# Função para posicionar a câmera automaticamente
def position_camera(objects):
    # Calcular a posição média dos objetos
    x_coords = [obj.location.x for obj in objects]
    y_coords = [obj.location.y for obj in objects]
    z_coords = [obj.location.z + obj.scale.z/2 for obj in objects]  # Considerando altura
  
    x_center = sum(x_coords) / len(x_coords)
    y_center = sum(y_coords) / len(y_coords)
    z_max = max(z_coords)  # Ponto mais alto
  
    # Ajustar posição da câmera
    camera = bpy.data.objects['Camera']
    camera.location.x = x_center
    camera.location.y = y_center - max(y_coords) * 2  # Distância suficiente para ver todos os objetos
    camera.location.z = z_max * 1.5  # Eleva a câmera para ver todos os objetos
  
    # Apontar a câmera para o centro dos objetos
    camera.rotation_euler = (1.1, 0, 0)  # Ajuste manual da rotação (X, Y, Z)
    camera.data.lens = 35  # Opcional: ajustar o campo de visão da câmera

# Carregar dados e criar objetos
data = load_json(filepath)
objects = create_objects_from_data(data)

# Posicionar a câmera com base nos objetos
position_camera(objects)
```

### 2. Explicação do Script:

- **Carregar e Criar Objetos:** O script cria os objetos e retorna uma lista dos objetos criados.
- **Calcular o Centro:** Calcula a posição média dos objetos (`x_center`, `y_center`) e o ponto mais alto (`z_max`).
- **Posicionar a Câmera:** Posiciona a câmera no centro dos objetos e ajusta sua altura para garantir que todos os objetos sejam visíveis.
- **Apontar a Câmera:** A câmera é orientada para olhar para o centro da cena.

### 3. Executar o Script

1. **Abra o Editor de Texto:** No UPBGE, abra o Editor de Texto e cole o script modificado.
2. **Executar o Script:** Pressione "Run Script" para criar os objetos e posicionar a câmera automaticamente.

### 4. Testar no Modo de Jogo

1. **Entrar no Modo de Jogo:** Pressione `P` para entrar no modo de jogo e verifique se a câmera está enquadrando todos os objetos corretamente.

### 5. Ajustes Finais

- **Campo de Visão:** Você pode ajustar o `camera.data.lens` para aumentar ou diminuir o campo de visão da câmera.
- **Rotação da Câmera:** Se necessário, ajuste manualmente a rotação da câmera para enquadrar melhor os objetos.

Agora, sua cena 3D no UPBGE não só cria objetos baseados nos dados JSON, mas também posiciona a câmera automaticamente para que todos os objetos sejam visíveis!

# Baseado no código anterior altere os dados json para que todos os objetos fiquem no mesmo y e z


Para modificar o JSON de modo que todos os objetos fiquem no mesmo `y` e `z`, você pode ajustar os valores desses eixos no arquivo JSON. Vou fornecer um exemplo de JSON atualizado onde todos os objetos têm a mesma coordenada `y` e `z`.

### 1. Exemplo de `data.json` com `y` e `z` fixos

```json
[
  {"name": "Item1", "x": 1, "y": 0, "z": 0, "height": 2.5},
  {"name": "Item2", "x": 4, "y": 0, "z": 0, "height": 1.8},
  {"name": "Item3", "x": 7, "y": 0, "z": 0, "height": 3.0},
  {"name": "Item4", "x": 10, "y": 0, "z": 0, "height": 2.0},
  {"name": "Item5", "x": 13, "y": 0, "z": 0, "height": 1.5}
]
```

### 2. Explicação do JSON:

- **`x`:** As posições `x` variam, o que significa que os objetos serão dispostos ao longo do eixo X.
- **`y`:** Todos os valores de `y` são `0`, então todos os objetos estarão alinhados ao longo do eixo Y.
- **`z`:** Todos os valores de `z` são `0`, então todos os objetos serão colocados na mesma altura inicial. A altura real dos objetos será determinada pelo valor `height`.

### 3. Usar o JSON Atualizado no Script Python

Você pode usar o JSON atualizado com o script anterior sem nenhuma modificação. O script criará os objetos no UPBGE, e todos estarão alinhados ao longo do eixo X, com a mesma altura inicial em `z = 0` e `y = 0`.

### 4. Executar o Script

1. **Certifique-se de que o JSON atualizado esteja salvo** com os valores acima.
2. **Execute o script Python** como descrito anteriormente para criar os objetos e posicionar a câmera.

### 5. Visualizar os Objetos

No modo de jogo (`P`), você verá todos os objetos alinhados em uma linha ao longo do eixo X, com diferentes alturas, mas na mesma posição `y` e `z`.

Se precisar de mais ajustes ou tiver outras perguntas, estou à disposição para ajudar!

# Adicionar no script uma luz que simule o dia ensolarado


Para adicionar uma luz que simule um dia ensolarado na cena, vamos utilizar uma luz do tipo `Sun` no UPBGE. A luz do tipo `Sun` emite luz paralela, semelhante à luz solar, e é ideal para iluminar toda a cena de forma uniforme.

### 1. Modificação do Script Python

Aqui está o script modificado para incluir a criação de uma luz solar (`Sun`) junto com os objetos e o posicionamento da câmera.

#### Script Python Modificado com Luz Solar:

```python
import bpy
import json

# Caminho para o arquivo JSON
filepath = "/caminho/para/o/arquivo/data.json"

# Função para carregar dados JSON
def load_json(filepath):
    with open(filepath, 'r') as file:
        return json.load(file)

# Função para criar objetos a partir dos dados, ajustando altura
def create_objects_from_data(data):
    objects = []
    for item in data:
        # Criar o objeto cubo
        bpy.ops.mesh.primitive_cube_add(size=1, location=(item['x'], item['y'], item['z']))
        obj = bpy.context.object
      
        # Definir nome do objeto
        obj.name = item['name']
      
        # Ajustar altura do cubo (alongar no eixo Z)
        obj.scale.z = item['height']
      
        # Adicionar o objeto à lista
        objects.append(obj)
  
    return objects

# Função para posicionar a câmera automaticamente
def position_camera(objects):
    # Calcular a posição média dos objetos
    x_coords = [obj.location.x for obj in objects]
    y_coords = [obj.location.y for obj in objects]
    z_coords = [obj.location.z + obj.scale.z/2 for obj in objects]  # Considerando altura
  
    x_center = sum(x_coords) / len(x_coords)
    y_center = sum(y_coords) / len(y_coords)
    z_max = max(z_coords)  # Ponto mais alto
  
    # Ajustar posição da câmera
    camera = bpy.data.objects['Camera']
    camera.location.x = x_center
    camera.location.y = y_center - max(y_coords) * 2  # Distância suficiente para ver todos os objetos
    camera.location.z = z_max * 1.5  # Eleva a câmera para ver todos os objetos
  
    # Apontar a câmera para o centro dos objetos
    camera.rotation_euler = (1.1, 0, 0)  # Ajuste manual da rotação (X, Y, Z)
    camera.data.lens = 35  # Opcional: ajustar o campo de visão da câmera

# Função para adicionar uma luz solar (Sun)
def add_sun_light():
    # Adicionar a luz do tipo Sun
    bpy.ops.object.light_add(type='SUN', radius=1, location=(10, -10, 10))
    sun = bpy.context.object
  
    # Configurar a rotação da luz para simular um dia ensolarado
    sun.rotation_euler = (0.7854, 0, 0.7854)  # Inclina a luz para simular o sol no céu
  
    # Ajustar a intensidade e cor da luz
    sun.data.energy = 5  # Intensidade da luz solar
    sun.data.color = (1, 1, 0.98)  # Levemente amarelada para simular o sol

# Carregar dados e criar objetos
data = load_json(filepath)
objects = create_objects_from_data(data)

# Posicionar a câmera com base nos objetos
position_camera(objects)

# Adicionar uma luz solar à cena
add_sun_light()
```

### 2. Explicação das Modificações:

- **Adicionar Luz Solar (`Sun`):** A função `add_sun_light()` adiciona uma luz do tipo `Sun` à cena.
- **Posicionamento da Luz:** A luz é posicionada no ponto `(10, -10, 10)`, o que simula o sol vindo de um ângulo.
- **Rotação da Luz:** A rotação da luz é ajustada para simular a iluminação solar em um dia ensolarado.
- **Configuração da Intensidade e Cor:** A intensidade da luz é aumentada para `5`, e a cor é ajustada para um tom levemente amarelado, que é típico da luz solar.

### 3. Executar o Script

1. **Abra o Editor de Texto:** No UPBGE, abra o Editor de Texto e cole o script modificado.
2. **Execute o Script:** Pressione "Run Script" para criar os objetos, posicionar a câmera e adicionar a luz solar.

### 4. Testar no Modo de Jogo

1. **Entrar no Modo de Jogo:** Pressione `P` para entrar no modo de jogo e verifique como a luz solar ilumina a cena.

### 5. Ajustes Finais

- **Intensidade da Luz:** Se a cena estiver muito clara ou escura, ajuste `sun.data.energy` para aumentar ou diminuir a intensidade da luz solar.
- **Rotação da Luz:** Ajuste `sun.rotation_euler` se quiser alterar o ângulo de incidência da luz.

Agora sua cena 3D no UPBGE simula um dia ensolarado, com luz solar natural iluminando todos os objetos!

# Adicionar um material metálico aos objetos do cena anterior


Para adicionar um material metálico aos objetos na cena do UPBGE, você pode modificar o script para incluir a criação e aplicação de um material com propriedades metálicas para cada objeto. Abaixo está o script atualizado que inclui essa funcionalidade.

### 1. Modificação do Script Python

Vamos adicionar uma função para criar um material metálico e aplicá-lo aos objetos criados a partir dos dados JSON.

#### Script Python Modificado com Material Metálico:

```python
import bpy
import json

# Caminho para o arquivo JSON
filepath = "/caminho/para/o/arquivo/data.json"

# Função para carregar dados JSON
def load_json(filepath):
    with open(filepath, 'r') as file:
        return json.load(file)

# Função para criar e aplicar um material metálico aos objetos
def create_metallic_material():
    # Criar um novo material
    material = bpy.data.materials.new(name="MetallicMaterial")
  
    # Ativar o uso de nodes
    material.use_nodes = True
  
    # Obter o nó de Principled BSDF (nó padrão)
    principled_bsdf = material.node_tree.nodes.get("Principled BSDF")
  
    # Configurar as propriedades do material para ser metálico
    principled_bsdf.inputs['Metallic'].default_value = 1.0  # Valor metálico máximo
    principled_bsdf.inputs['Roughness'].default_value = 0.3  # Controle de rugosidade (menos rugoso = mais brilhante)
    principled_bsdf.inputs['Base Color'].default_value = (0.8, 0.8, 0.8, 1)  # Cor base (cinza claro)
  
    return material

# Função para criar objetos a partir dos dados, ajustando altura e aplicando material
def create_objects_from_data(data, material):
    objects = []
    for item in data:
        # Criar o objeto cubo
        bpy.ops.mesh.primitive_cube_add(size=1, location=(item['x'], item['y'], item['z']))
        obj = bpy.context.object
      
        # Definir nome do objeto
        obj.name = item['name']
      
        # Ajustar altura do cubo (alongar no eixo Z)
        obj.scale.z = item['height']
      
        # Aplicar o material metálico ao objeto
        if obj.data.materials:
            obj.data.materials[0] = material
        else:
            obj.data.materials.append(material)
      
        # Adicionar o objeto à lista
        objects.append(obj)
  
    return objects

# Função para posicionar a câmera automaticamente
def position_camera(objects):
    # Calcular a posição média dos objetos
    x_coords = [obj.location.x for obj in objects]
    y_coords = [obj.location.y for obj in objects]
    z_coords = [obj.location.z + obj.scale.z/2 for obj in objects]  # Considerando altura
  
    x_center = sum(x_coords) / len(x_coords)
    y_center = sum(y_coords) / len(y_coords)
    z_max = max(z_coords)  # Ponto mais alto
  
    # Ajustar posição da câmera
    camera = bpy.data.objects['Camera']
    camera.location.x = x_center
    camera.location.y = y_center - max(y_coords) * 2  # Distância suficiente para ver todos os objetos
    camera.location.z = z_max * 1.5  # Eleva a câmera para ver todos os objetos
  
    # Apontar a câmera para o centro dos objetos
    camera.rotation_euler = (1.1, 0, 0)  # Ajuste manual da rotação (X, Y, Z)
    camera.data.lens = 35  # Opcional: ajustar o campo de visão da câmera

# Função para adicionar uma luz solar (Sun)
def add_sun_light():
    # Adicionar a luz do tipo Sun
    bpy.ops.object.light_add(type='SUN', radius=1, location=(10, -10, 10))
    sun = bpy.context.object
  
    # Configurar a rotação da luz para simular um dia ensolarado
    sun.rotation_euler = (0.7854, 0, 0.7854)  # Inclina a luz para simular o sol no céu
  
    # Ajustar a intensidade e cor da luz
    sun.data.energy = 5  # Intensidade da luz solar
    sun.data.color = (1, 1, 0.98)  # Levemente amarelada para simular o sol

# Carregar dados e criar material metálico
data = load_json(filepath)
metallic_material = create_metallic_material()

# Criar objetos e aplicar o material metálico
objects = create_objects_from_data(data, metallic_material)

# Posicionar a câmera com base nos objetos
position_camera(objects)

# Adicionar uma luz solar à cena
add_sun_light()
```

### 2. Explicação das Modificações:

- **Criar Material Metálico:** A função `create_metallic_material()` cria um novo material com propriedades metálicas, usando o shader `Principled BSDF`.

  - **Metallic:** Configurado para 1.0, que define o material como completamente metálico.
  - **Roughness:** Configurado para 0.3, o que dá ao material uma aparência moderadamente brilhante.
  - **Base Color:** Definido como um cinza claro, que é típico para metais como o aço inoxidável.
- **Aplicar Material:** Durante a criação dos objetos, o material metálico é aplicado a cada objeto.

### 3. Executar o Script

1. **Abra o Editor de Texto:** No UPBGE, abra o Editor de Texto e cole o script modificado.
2. **Execute o Script:** Pressione "Run Script" para criar os objetos, posicionar a câmera, adicionar a luz solar e aplicar o material metálico.

### 4. Testar no Modo de Jogo

1. **Entrar no Modo de Jogo:** Pressione `P` para entrar no modo de jogo e visualizar os objetos com o material metálico aplicado.

### 5. Ajustes Finais

- **Cor do Material:** Se desejar, você pode ajustar a `Base Color` do material metálico para diferentes tons de metal, como cobre, ouro, etc.
- **Roughness:** Aumentar ou diminuir o valor de `Roughness` afetará o brilho e a nitidez dos reflexos.

Agora, os objetos na sua cena 3D no UPBGE terão um material metálico aplicado, com iluminação simulando um dia ensolarado.

# Adicionar um terreno


Para adicionar um terreno na sua cena 3D no UPBGE, você pode criar um plano grande que representará o chão da cena e aplicar um material a esse plano para dar a aparência de terreno. Aqui está como você pode modificar o script para adicionar esse terreno.

### 1. Modificação do Script Python

Vamos adicionar uma função para criar o terreno e aplicar um material básico a ele.

#### Script Python Modificado com Terreno:

```python
import bpy
import json

# Caminho para o arquivo JSON
filepath = "/caminho/para/o/arquivo/data.json"

# Função para carregar dados JSON
def load_json(filepath):
    with open(filepath, 'r') as file:
        return json.load(file)

# Função para criar e aplicar um material metálico aos objetos
def create_metallic_material():
    material = bpy.data.materials.new(name="MetallicMaterial")
    material.use_nodes = True
  
    principled_bsdf = material.node_tree.nodes.get("Principled BSDF")
    principled_bsdf.inputs['Metallic'].default_value = 1.0
    principled_bsdf.inputs['Roughness'].default_value = 0.3
    principled_bsdf.inputs['Base Color'].default_value = (0.8, 0.8, 0.8, 1)
  
    return material

# Função para criar objetos a partir dos dados, ajustando altura e aplicando material
def create_objects_from_data(data, material):
    objects = []
    for item in data:
        bpy.ops.mesh.primitive_cube_add(size=1, location=(item['x'], item['y'], item['z']))
        obj = bpy.context.object
      
        obj.name = item['name']
        obj.scale.z = item['height']
      
        if obj.data.materials:
            obj.data.materials[0] = material
        else:
            obj.data.materials.append(material)
      
        objects.append(obj)
  
    return objects

# Função para criar o terreno
def create_terrain():
    # Adicionar um plano que servirá como terreno
    bpy.ops.mesh.primitive_plane_add(size=50, location=(0, 0, 0))
    terrain = bpy.context.object
  
    # Criar um material para o terreno
    terrain_material = bpy.data.materials.new(name="TerrainMaterial")
    terrain_material.use_nodes = True
  
    # Obter o nó de Principled BSDF
    principled_bsdf = terrain_material.node_tree.nodes.get("Principled BSDF")
  
    # Configurar a cor e a rugosidade do terreno
    principled_bsdf.inputs['Base Color'].default_value = (0.3, 0.2, 0.1, 1)  # Marrom
    principled_bsdf.inputs['Roughness'].default_value = 0.8  # Mais rugoso
  
    # Aplicar o material ao plano
    if terrain.data.materials:
        terrain.data.materials[0] = terrain_material
    else:
        terrain.data.materials.append(terrain_material)
  
    return terrain

# Função para posicionar a câmera automaticamente
def position_camera(objects):
    x_coords = [obj.location.x for obj in objects]
    y_coords = [obj.location.y for obj in objects]
    z_coords = [obj.location.z + obj.scale.z/2 for obj in objects]
  
    x_center = sum(x_coords) / len(x_coords)
    y_center = sum(y_coords) / len(y_coords)
    z_max = max(z_coords)
  
    camera = bpy.data.objects['Camera']
    camera.location.x = x_center
    camera.location.y = y_center - max(y_coords) * 2
    camera.location.z = z_max * 1.5
  
    camera.rotation_euler = (1.1, 0, 0)
    camera.data.lens = 35

# Função para adicionar uma luz solar (Sun)
def add_sun_light():
    bpy.ops.object.light_add(type='SUN', radius=1, location=(10, -10, 10))
    sun = bpy.context.object
  
    sun.rotation_euler = (0.7854, 0, 0.7854)
    sun.data.energy = 5
    sun.data.color = (1, 1, 0.98)

# Carregar dados e criar material metálico
data = load_json(filepath)
metallic_material = create_metallic_material()

# Criar o terreno
terrain = create_terrain()

# Criar objetos e aplicar o material metálico
objects = create_objects_from_data(data, metallic_material)

# Posicionar a câmera com base nos objetos
position_camera(objects)

# Adicionar uma luz solar à cena
add_sun_light()
```

### 2. Explicação das Modificações:

- **Função `create_terrain`:** Esta função cria um plano grande (tamanho 50) que serve como terreno.

  - **Material do Terreno:** Um material simples é criado e aplicado ao plano, com uma cor marrom e rugosidade alta para simular terra.
- **Adição do Terreno na Cena:** O terreno é criado antes dos objetos para garantir que os objetos sejam posicionados sobre ele.

### 3. Executar o Script

1. **Abra o Editor de Texto:** No UPBGE, abra o Editor de Texto e cole o script modificado.
2. **Execute o Script:** Pressione "Run Script" para criar o terreno, os objetos, posicionar a câmera e adicionar a luz solar.

### 4. Testar no Modo de Jogo

1. **Entrar no Modo de Jogo:** Pressione `P` para entrar no modo de jogo e visualizar o terreno com os objetos sobre ele.

### 5. Ajustes Finais

- **Textura do Terreno:** Para um terreno mais realista, você pode adicionar uma textura de solo ao material do plano.
- **Tamanho do Plano:** O tamanho do plano pode ser ajustado conforme necessário para cobrir toda a cena.

Agora, sua cena no UPBGE inclui um terreno básico que serve de base para os objetos.

# Adicionar um céu


Para adicionar um céu à sua cena no UPBGE, você pode utilizar uma técnica simples chamada "Sky Dome" ou "Sky Sphere". Essa técnica envolve a criação de uma grande esfera ao redor da cena, onde você pode aplicar uma textura de céu. Outra alternativa seria usar o recurso de "World Settings" do Blender para configurar um céu.

### 1. Adicionando um Céu usando uma Esfera (Sky Dome)

Primeiro, vamos adicionar uma esfera invertida como uma "Sky Dome" e aplicar uma textura de céu.

#### Script Python Modificado com Sky Dome:

```python
import bpy
import json

# Caminho para o arquivo JSON
filepath = "/caminho/para/o/arquivo/data.json"

# Função para carregar dados JSON
def load_json(filepath):
    with open(filepath, 'r') as file:
        return json.load(file)

# Função para criar e aplicar um material metálico aos objetos
def create_metallic_material():
    material = bpy.data.materials.new(name="MetallicMaterial")
    material.use_nodes = True
  
    principled_bsdf = material.node_tree.nodes.get("Principled BSDF")
    principled_bsdf.inputs['Metallic'].default_value = 1.0
    principled_bsdf.inputs['Roughness'].default_value = 0.3
    principled_bsdf.inputs['Base Color'].default_value = (0.8, 0.8, 0.8, 1)
  
    return material

# Função para criar objetos a partir dos dados, ajustando altura e aplicando material
def create_objects_from_data(data, material):
    objects = []
    for item in data:
        bpy.ops.mesh.primitive_cube_add(size=1, location=(item['x'], item['y'], item['z']))
        obj = bpy.context.object
      
        obj.name = item['name']
        obj.scale.z = item['height']
      
        if obj.data.materials:
            obj.data.materials[0] = material
        else:
            obj.data.materials.append(material)
      
        objects.append(obj)
  
    return objects

# Função para criar o terreno
def create_terrain():
    bpy.ops.mesh.primitive_plane_add(size=50, location=(0, 0, 0))
    terrain = bpy.context.object
  
    terrain_material = bpy.data.materials.new(name="TerrainMaterial")
    terrain_material.use_nodes = True
  
    principled_bsdf = terrain_material.node_tree.nodes.get("Principled BSDF")
    principled_bsdf.inputs['Base Color'].default_value = (0.3, 0.2, 0.1, 1)  # Marrom
    principled_bsdf.inputs['Roughness'].default_value = 0.8  # Mais rugoso
  
    if terrain.data.materials:
        terrain.data.materials[0] = terrain_material
    else:
        terrain.data.materials.append(terrain_material)
  
    return terrain

# Função para criar o céu (Sky Dome)
def create_sky_dome():
    # Criar uma esfera grande para representar o céu
    bpy.ops.mesh.primitive_uv_sphere_add(radius=100, location=(0, 0, 0))
    sky_dome = bpy.context.object
  
    # Inverter as normais para que a esfera seja visível de dentro (Sky Dome)
    bpy.ops.object.mode_set(mode='EDIT')
    bpy.ops.mesh.select_all(action='SELECT')
    bpy.ops.mesh.flip_normals()
    bpy.ops.object.mode_set(mode='OBJECT')
  
    # Criar um material para o céu
    sky_material = bpy.data.materials.new(name="SkyMaterial")
    sky_material.use_nodes = True
  
    # Adicionar uma textura ao material do céu
    bsdf = sky_material.node_tree.nodes["Principled BSDF"]
    tex_image = sky_material.node_tree.nodes.new('ShaderNodeTexImage')
  
    # Carregar uma imagem de textura de céu (substitua pelo caminho da sua textura)
    tex_image.image = bpy.data.images.load("/caminho/para/sua/textura_de_ceu.jpg")
  
    # Conectar a textura ao material
    sky_material.node_tree.links.new(bsdf.inputs['Base Color'], tex_image.outputs['Color'])
  
    # Aplicar o material à esfera
    sky_dome.data.materials.append(sky_material)
  
    return sky_dome

# Função para posicionar a câmera automaticamente
def position_camera(objects):
    x_coords = [obj.location.x for obj in objects]
    y_coords = [obj.location.y for obj in objects]
    z_coords = [obj.location.z + obj.scale.z/2 for obj in objects]
  
    x_center = sum(x_coords) / len(x_coords)
    y_center = sum(y_coords) / len(y_coords)
    z_max = max(z_coords)
  
    camera = bpy.data.objects['Camera']
    camera.location.x = x_center
    camera.location.y = y_center - max(y_coords) * 2
    camera.location.z = z_max * 1.5
  
    camera.rotation_euler = (1.1, 0, 0)
    camera.data.lens = 35

# Função para adicionar uma luz solar (Sun)
def add_sun_light():
    bpy.ops.object.light_add(type='SUN', radius=1, location=(10, -10, 10))
    sun = bpy.context.object
  
    sun.rotation_euler = (0.7854, 0, 0.7854)
    sun.data.energy = 5
    sun.data.color = (1, 1, 0.98)

# Carregar dados e criar material metálico
data = load_json(filepath)
metallic_material = create_metallic_material()

# Criar o terreno
terrain = create_terrain()

# Criar o céu (Sky Dome)
sky_dome = create_sky_dome()

# Criar objetos e aplicar o material metálico
objects = create_objects_from_data(data, metallic_material)

# Posicionar a câmera com base nos objetos
position_camera(objects)

# Adicionar uma luz solar à cena
add_sun_light()
```

### 2. Explicação das Modificações:

- **Função `create_sky_dome`:** Esta função cria uma esfera grande (`radius=100`) que representa o céu ao redor da cena.
  - **Inversão das Normais:** As normais da esfera são invertidas para que a superfície interna seja visível, criando um efeito de "Sky Dome".
  - **Material do Céu:** Um material é criado e uma textura de céu é aplicada a ele.
  - **Textura de Céu:** Substitua `"/caminho/para/sua/textura_de_ceu.jpg"` pelo caminho da sua textura de céu.

### 3. Usar World Settings para Configurar o Céu (Alternativa)

Se preferir usar o "World Settings" do Blender, aqui está como você pode configurar:

1. **Criar um Mundo:** Em vez de adicionar um Sky Dome, você pode configurar o céu diretamente no "World Settings".
2. **Adicionar um Background:** No painel de "World", selecione `Use Nodes` e configure o `Background` para um tom de azul ou adicione uma textura de céu.

### 4. Executar o Script

1. **Abra o Editor de Texto:** No UPBGE, abra o Editor de Texto e cole o script modificado.
2. **Execute o Script:** Pressione "Run Script" para criar o céu, o terreno, os objetos, posicionar a câmera e adicionar a luz solar.

### 5. Testar no Modo de Jogo

1. **Entrar no Modo de Jogo:** Pressione `P` para entrar no modo de jogo e visualizar o céu ao redor da sua cena.

Agora, sua cena 3D no UPBGE inclui um céu usando um "Sky Dome" ou configurando o "World Settings".
