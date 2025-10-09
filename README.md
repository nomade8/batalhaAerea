# Simulador de Voo - Spitfire

Este projeto implementa um simulador de voo básico de um avião Spitfire utilizando a biblioteca Three.js para renderização 3D no navegador.

## Estrutura do Projeto

O projeto consiste em um único arquivo `index.html` que contém toda a estrutura HTML, estilos CSS e o código JavaScript para a simulação.

## Principais Componentes e Funções

A seguir, uma descrição das principais partes do código JavaScript:

### 1. Configuração Inicial (Linhas 40-55)
- **`scene`**: Objeto principal onde todos os elementos 3D são adicionados. O fundo é definido com uma cor azul claro (`0x87ceeb`).
- **`camera`**: Câmera de perspectiva que define o ponto de vista do jogador. Configurada com um campo de visão de 75 graus e planos de corte próximos e distantes para renderizar objetos de 0.001 a 20000 unidades de distância.
- **`renderer`**: Responsável por renderizar a cena na tela. Configurado para usar antialiasing, sombras e correção de cores SRGB.

### 2. Iluminação (Linhas 58-65)
- **`hemiLight` (HemisphereLight)**: Simula a luz ambiente, com cores do céu e do chão. Reduzida para uma intensidade de 0.1.
- **`dirLight` (DirectionalLight)**: Simula a luz solar, com uma intensidade de 0.2. Posicionada para lançar sombras.

### 3. Mundo (Céu + Terreno) (Linhas 68-158)
- **`world`**: Um grupo `THREE.Group` que contém o terreno e outros elementos do cenário.
- **`terrainGroup`**: Grupo específico para o terreno, permitindo manipulação conjunta.
- **Geração de Terreno**: Um `PlaneGeometry` é usado para criar o terreno, e suas alturas são ajustadas usando uma combinação de funções seno e cosseno para criar um relevo suave (colinas e montanhas leves).
- **Pista de Pouso**: Uma área plana dentro do terreno é definida para a pista, com uma geometria e material específicos. Inclui uma marcação central.
- **Cidade**: Um grupo de caixas (`BoxGeometry`) com cores variadas é gerado para simular uma pequena cidade ao lado da pista.

### 4. Avião (Spitfire) e Câmera (Linhas 161-197)
- **`GLTFLoader`**: Usado para carregar o modelo 3D do avião (`spitfire.glb`).
- **`aircraft`**: Um `THREE.Group` que representa o avião. O modelo carregado é adicionado a este grupo.
- **`AxesHelper`**: Adicionado ao avião para depuração, mostrando os eixos X (vermelho), Y (verde) e Z (azul) do modelo.
- **Câmera no Cockpit**: A câmera é posicionada dentro do grupo `aircraft` com um deslocamento (`cockpitOffset`) para simular a visão de dentro do cockpit.

### 5. Controles (Linhas 200-222)
- **`keys`**: Objeto que rastreia o estado das teclas de seta (pressionadas ou não).
- **`keydown` e `keyup` listeners**: Event listeners para detectar quando as teclas de seta são pressionadas ou soltas, atualizando o estado no objeto `keys`.

### 6. Parâmetros de Movimento (Linhas 225-233)
- **`speed`**: Velocidade de avanço do avião (unidades/segundo).
- **`yawRate`**: Taxa de guinada (rotação em torno do eixo Y) em radianos por segundo.
- **`pitchRate`**: Taxa de arfagem (rotação em torno do eixo X) em radianos por segundo.
- **`bank`**: Inclinação lateral do avião.
- **`maxBank`**: Inclinação lateral máxima permitida.
- **`bankSmoothing`**: Fator de suavização para a inclinação lateral.

### 7. Hierarquia de Objetos e Controles

**Estrutura Hierárquica:**
- `aircraftGroup` (contêiner principal): Recebe todas as transformações de voo (posição e rotações)
- `aircraft` (modelo): Filho do aircraftGroup, contém o modelo GLTF
- `camera` (cockpit): Filha do aircraftGroup, posicionada no cockpit com offset

**Controles (aplicados ao aircraftGroup usando eixos locais):**
- **Roll**: Teclas Esquerda/Direita controlam rotação direta no eixo Z local (forward) usando `rotateOnAxis(forwardAxis, rollRate * dt)`
- **Pitch**: Teclas Cima/Baixo controlam rotação no eixo X local (right) usando `rotateOnAxis(rightAxis, pitchRate * dt)`
- **Yaw Automático**: Guinada proporcional ao roll atual: `rotateOnAxis(upAxis, -rollAngle * turnFactor * dt)`
- **Movimento**: Sempre na direção do vetor frontal do aircraftGroup: `forwardVector.applyQuaternion(aircraftGroup.quaternion)`

### 8. Loop de Animação (`animate` function) (Linhas 239-319)
- **`requestAnimationFrame(animate)`**: Chama a função `animate` repetidamente para criar o loop de renderização.
- **`dt`**: Delta time, o tempo decorrido desde o último frame, usado para garantir que o movimento seja independente da taxa de quadros.
- **Terreno Fixo**: O `terrainGroup` tem sua posição X e Z mantida em 0, garantindo que o terreno permaneça fixo no mundo e o avião se mova sobre ele.
- **Atualização da HUD**: A velocidade do avião é exibida na interface (HUD).
- **`renderer.render(scene, camera)`**: Renderiza a cena a cada frame.

### 8. Redimensionamento da Janela (Linhas 321-325)
- **`resize` event listener**: Ajusta a proporção da câmera e o tamanho do renderizador quando a janela do navegador é redimensionada, garantindo que a visualização permaneça correta.

## Como Executar o Projeto

1. Certifique-se de ter um servidor web local (como `npx serve` ou Live Server do VS Code).
2. Navegue até o diretório do projeto.
3. Abra o `index.html` através do seu servidor web (ex: `http://localhost:5174`).

## Controles

- **Setas Esquerda/Direita**: Roll (rotação no eixo Z local/forward) - inclinação lateral direta
- **Setas Cima/Baixo**: Pitch (rotação no eixo X local/right) - Cima abaixa o nariz, Baixo levanta o nariz
- **Yaw**: Automático, proporcional ao roll atual para simular viragem aerodinâmica