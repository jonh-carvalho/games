# Tecido

Para criar uma cena no Blender onde um tecido (um plano) cai sobre um cubo usando simulação de física de pano (cloth), siga estes passos:

### Passo 1: Configuração da Cena Básica

1. **Abra o Blender**.
2. **Remova os objetos padrão** (geralmente um cubo). Selecione o cubo, pressione `X` e clique em "Delete".
3. **Adicione um novo cubo**:

   - Pressione `Shift + A` para abrir o menu de adição.
   - Selecione `Mesh > Cube`.
   - Posicione o cubo em `Z = 0` para ficar na superfície da grade.
4. **Adicione um plano (Plane) para ser o tecido**:

   - Pressione `Shift + A` novamente e selecione `Mesh > Plane`.
   - Posicione o plano acima do cubo (em `Z = 2` por exemplo).

### Passo 2: Configuração da Física do Cubo

1. **Selecione o cubo**.
2. No painel de propriedades, vá para a aba de `Physics` (ícone de círculo com pontos dentro) e clique em `Collision`.
   - Isso faz com que o cubo interaja com o tecido.

### Passo 3: Configuração da Física do Tecido

1. **Selecione o plano** (que será o tecido).
2. No painel de propriedades, vá para a aba de `Physics` e clique em `Cloth`.
   - Aqui você pode ajustar as propriedades como massa, esticamento, flexão, etc., para definir o comportamento do tecido.

### Passo 4: Ajuste da Resolução do Plano (Opcional)

1. **Subdivida o plano** para que o tecido tenha mais pontos para a simulação:
   - Selecione o plano.
   - Pressione `Tab` para entrar no Modo de Edição.
   - Pressione `Right Click` e selecione `Subdivide`.
   - No painel inferior esquerdo, aumente o número de cortes para dar mais detalhe ao tecido.
   - Pressione `Tab` novamente para sair do Modo de Edição.

### Passo 5: Simulação

1. **Inicie a simulação**:
   - Pressione `Space` ou `Shift + Space` para iniciar a animação.
   - O plano (tecido) deve cair sobre o cubo e reagir como um tecido real.

### Passo 6: Ajustes Finais

1. **Ajuste o comportamento** da simulação na aba `Cloth` se necessário.
2. **Adicione uma superfície** de chão (novo plano abaixo do cubo) e marque como `Collision` para que o tecido não caia indefinidamente.

### Passo 7: Renderização

1. **Configure a iluminação** e a câmera para a cena.
2. **Renderize a animação** ou um frame específico da queda do tecido.

Essa configuração simples deve permitir que o tecido caia e se deforme ao colidir com o cubo, criando uma cena realista de simulação física no Blender.



Para criar essa cena no Blender usando um script Python, você pode seguir o roteiro abaixo. Esse script cria um cubo, adiciona um plano como tecido, configura a simulação de física e executa a animação.

```python
import bpy

# Limpa a cena padrão
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete(use_global=False)

# Adiciona um cubo
bpy.ops.mesh.primitive_cube_add(size=2, location=(0, 0, 1))

# Pega o objeto recém-criado (o cubo)
cube = bpy.context.object

# Adiciona a física de colisão ao cubo
bpy.ops.object.modifier_add(type='COLLISION')

# Adiciona um plano (plane) para ser o tecido
bpy.ops.mesh.primitive_plane_add(size=4, location=(0, 0, 4))
plane = bpy.context.object

# Subdivide o plano para aumentar a resolução
bpy.ops.object.mode_set(mode='EDIT')
bpy.ops.mesh.subdivide(number_cuts=10)
bpy.ops.object.mode_set(mode='OBJECT')

# Adiciona a física de cloth ao plano
bpy.ops.object.modifier_add(type='CLOTH')

# Configurações adicionais para a simulação de tecido
cloth_mod = plane.modifiers["Cloth"]
cloth_mod.settings.quality = 5  # Qualidade da simulação
cloth_mod.settings.mass = 0.3  # Massa do tecido

# Adiciona um chão abaixo do cubo para evitar queda infinita do tecido
bpy.ops.mesh.primitive_plane_add(size=10, location=(0, 0, 0))
floor = bpy.context.object

# Adiciona física de colisão ao chão
bpy.ops.object.modifier_add(type='COLLISION')

# Configurações de cena e animação
bpy.context.scene.frame_start = 1
bpy.context.scene.frame_end = 250

# Executa a animação (opcional, se você quiser simular direto)
bpy.ops.ptcache.bake_all(bake=True)
```

### Explicação do Código:

1. **Limpeza da Cena**:

   - O código inicial limpa todos os objetos da cena padrão.
2. **Criação e Configuração do Cubo**:

   - Adiciona um cubo na cena, o eleva do solo e adiciona um modificador de colisão para que ele interaja com o tecido.
3. **Criação e Configuração do Tecido**:

   - Adiciona um plano, o posiciona acima do cubo, subdivide para melhorar a qualidade da simulação e aplica o modificador `Cloth` para ativar a simulação de tecido.
4. **Criação do Chão**:

   - Adiciona um plano abaixo do cubo para agir como chão e garantir que o tecido não caia indefinidamente.
5. **Configuração de Simulação**:

   - Define o intervalo de quadros para a animação e realiza a simulação imediatamente (o `bake`).

### Como Executar o Script:

1. **Abra o Blender**.
2. **Vá para o Editor de Textos** (`Scripting`) no topo.
3. **Crie um novo script** e cole o código acima.
4. **Clique em "Run Script"** para executar e criar a cena automaticamente.

Isso criará a simulação no Blender conforme descrito.
