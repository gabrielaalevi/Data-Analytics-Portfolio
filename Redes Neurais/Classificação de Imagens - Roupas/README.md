## :dart: Descrição do Projeto

Neste projeto, utilizei o dataset conhecido como Fashion MNIST. Trata-se de um dataset de 70.000 imagens de roupas em preto e branco, separados em categorias como 'camisetas', 'vestidos', 'casacos', entre outras. Medu objetivo foi construir um modelo de redes neurais que pudesse classificar as imagens de forma automática. Para isso, utilizei a biblioteca PyTorch.
O banco de dados contém as seguintes colunas:

| Variável | Tipo | Descrição |
| -------- | ---- | ----------- |
|**Foto** | imagem | Foto da peça de roupa. |
|**Categoria** | str | Categoria de roupas a qual a peça pertence.|


Algumas das técnicas que apliquei neste projeto são:

- Train-Test Split para validação adequada dos modelos
- Max Pooling
- Redes Convolucionais
- Conceitos de Programação Orientada a Objetos (POO)
- Matriz de confusão, para avaliar o desempenho do modelo

Como próximos passos, o projeto pode ser aprimorado por meio de técnicas de Data Augmentation, aumentando a variedade das imagens utilizadas no treinamento e contribuindo para uma melhor generalização do modelo. Também seria interessante testar diferentes arquiteturas de Redes Neurais Convolucionais (CNNs) e ajustar hiperparâmetros, como taxa de aprendizado, tamanho do batch e número de épocas. Por fim, o modelo poderia ser disponibilizado em uma aplicação simples, permitindo que o usuário envie uma imagem de uma peça de roupa e receba sua classificação.
