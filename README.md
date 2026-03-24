# Sistema de Vendas - API

## Descrição do Projeto

O **Sistema de Vendas** é uma API RESTful desenvolvida em Java utilizando o framework Spring Boot. O sistema é projetado para gerenciar operações de vendas de uma empresa, incluindo o cadastro de clientes, produtos, pedidos, pagamentos e funcionários. Ele incorpora funcionalidades avançadas como sistema de fidelidade com categorias de clientes baseadas em compras, controle de estoque, e autenticação básica via Spring Security.

O projeto segue uma arquitetura em camadas (MVC - Model-View-Controller), com separação clara entre entidades, repositórios, fachadas (facades), aplicações (services) e controladores. Utiliza JPA/Hibernate para persistência de dados e MySQL como banco de dados.

## Funcionalidades Principais

- **Gerenciamento de Clientes**: Cadastro, atualização, busca e exclusão de clientes. Sistema de categorias de fidelidade (Bronze, Prata, Ouro, Diamante) baseado no valor total de compras nos últimos 90 dias.
- **Gerenciamento de Produtos**: Controle de estoque com alertas de mínimo, cadastro de produtos com código de barras, nome, descrição, preço e quantidade em estoque.
- **Gerenciamento de Pedidos**: Criação e gerenciamento de pedidos, incluindo itens do pedido, cálculo automático de valores totais, aplicação de descontos de fidelidade e atualização de status de pagamento.
- **Pagamentos**: Registro de pagamentos associados a pedidos, com suporte a diferentes tipos de pagamento e atualização automática do status do pedido.
- **Funcionários e Cargos**: Cadastro de funcionários e seus respectivos cargos.
- **Usuários e Autenticação**: Sistema básico de usuários com autenticação via Spring Security (usuário padrão: admin/admin).
- **Descontos de Fidelidade**: Aplicação automática de descontos baseados na categoria do cliente.

## Tecnologias Utilizadas

- **Java 17**: Linguagem de programação principal.
- **Spring Boot 3.4.0**: Framework para desenvolvimento de aplicações Java.
- **Spring Data JPA**: Para acesso e persistência de dados.
- **Spring Security**: Para autenticação e autorização básica.
- **Spring Web**: Para criação da API REST.
- **MySQL**: Banco de dados relacional.
- **Maven**: Gerenciamento de dependências e build.
- **Hibernate**: ORM para mapeamento objeto-relacional.

## Arquitetura do Projeto

O projeto segue uma arquitetura em camadas bem definida:

### Camadas

1. **Controller**: Responsável por expor os endpoints REST e receber as requisições HTTP.
2. **Facade**: Camada de serviço que orquestra as operações de negócio, atuando como interface entre controladores e aplicações.
3. **Application**: Contém a lógica de negócio específica para cada entidade.
4. **Repository**: Interface para acesso aos dados, utilizando Spring Data JPA.
5. **Entity**: Representação das tabelas do banco de dados em objetos Java.

### Estrutura de Pacotes

```
src/main/java/com/example/SistemaDeVendas/
├── SistemaDeVendasApplication.java          # Classe principal da aplicação
├── applications/                             # Lógica de negócio (services)
│   ├── CargoApplication.java
│   ├── ClienteApplication.java
│   ├── DescontoFidelidadeApplication.java
│   ├── FuncionarioApplication.java
│   ├── ItemPedidoApplication.java
│   ├── PagamentoApplication.java
│   ├── PedidoApplication.java
│   ├── ProdutoApplication.java
│   ├── TipoPagamentoApplication.java
│   └── UsuarioApplication.java
├── configs/                                  # Configurações da aplicação
│   ├── RegraNegocioException.java
│   └── SecurityConfig.java
├── controller/                               # Controladores REST
│   ├── CargoController.java
│   ├── ClienteController.java
│   ├── DescontoFidelidadeController.java
│   ├── FuncionarioController.java
│   ├── ItemPedidoController.java
│   ├── PagamentoController.java
│   ├── PedidoController.java
│   ├── ProdutoController.java
│   ├── TipoPagamentoController.java
│   └── UsuarioController.java
├── entities/                                 # Entidades JPA
│   ├── Cargo.java
│   ├── Categorias.java (Enum)
│   ├── Cliente.java
│   ├── DescontoFidelidade.java
│   ├── Funcionario.java
│   ├── ItemPedido.java
│   ├── Pagamento.java
│   ├── Pedido.java
│   ├── PedidosRecord.java
│   ├── Produto.java
│   ├── Roles.java (Enum)
│   ├── StatusPedido.java (Enum)
│   ├── TipoPagamento.java
│   └── Usuario.java
├── facades/                                  # Fachadas (orquestradores)
│   ├── CargoFacade.java
│   ├── ClienteFacade.java
│   ├── DescontoFidelidadeFacade.java
│   ├── FuncionarioFacade.java
│   ├── ItemPedidoFacade.java
│   ├── PagamentoFacade.java
│   ├── PedidoFacade.java
│   └── ProdutoFacade.java
├── interfacies/                              # Interfaces (vazio no projeto atual)
└── repositories/                             # Repositórios JPA
    ├── CargoRepository.java
    ├── ClienteRepository.java
    ├── DescontoFidelidadeRepository.java
    ├── FuncionarioRepository.java
    ├── ItemPedidoRepository.java
    ├── PagamentoRepository.java
    ├── PedidoRepository.java
    ├── ProdutoRepository.java
    ├── TipoPagamentoRepository.java
    └── UsuarioRepository.java
```

## Modelo de Dados

### Entidades Principais

#### Cliente
- `id`: Identificador único (auto-incremento)
- `nome`: Nome do cliente
- `cpf`: CPF único
- `telefone`: Telefone de contato
- `email`: Email do cliente
- `endereco`: Endereço completo
- `categoria`: Categoria de fidelidade (SEM_CATEGORIA, BRONZE, PRATA, OURO, DIAMANTE)
- Relacionamentos: Um-para-muitos com Pedido e DescontoFidelidade

#### Produto
- `id`: Identificador único
- `codigoBarras`: Código de barras do produto
- `nome`: Nome do produto
- `descricao`: Descrição detalhada
- `preco`: Preço unitário
- `estoque`: Quantidade em estoque
- `minEstoque`: Quantidade mínima para alerta
- Relacionamentos: Um-para-muitos com ItemPedido

#### Pedido
- `id`: Identificador único
- `dataPedido`: Data do pedido
- `cliente`: Cliente associado (muitos-para-um)
- `valorTotal`: Valor total do pedido
- `usuario`: Usuário que registrou o pedido
- `status`: Status do pedido (PENDENTE, PARCIALMENTE_PAGO, PAGO, CANCELADO)
- `descontoFidelidade`: Desconto aplicado (um-para-um)
- Relacionamentos: Um-para-muitos com Pagamento e ItemPedido

#### ItemPedido
- `id`: Identificador único
- `pedido`: Pedido associado
- `produto`: Produto do item
- `quantidade`: Quantidade do produto no pedido
- `precoUnitario`: Preço unitário no momento da venda

#### Pagamento
- `id`: Identificador único
- `pedido`: Pedido associado
- `tipoPagamento`: Tipo de pagamento
- `valor`: Valor do pagamento
- `dataPagamento`: Data do pagamento

#### Outras Entidades
- **Usuario**: Usuários do sistema
- **Funcionario**: Funcionários da empresa
- **Cargo**: Cargos dos funcionários
- **TipoPagamento**: Tipos de pagamento disponíveis
- **DescontoFidelidade**: Descontos baseados na fidelidade

## API Endpoints

A API expõe endpoints REST para todas as entidades principais. Cada controlador segue o padrão CRUD básico.

### Base URL
```
http://localhost:8080
```

### Autenticação
A aplicação utiliza Spring Security com autenticação básica. Credenciais padrão:
- **Usuário**: admin
- **Senha**: admin

Para acessar os endpoints, inclua o header de autorização:
```
Authorization: Basic YWRtaW46YWRtaW4=
```

### Endpoints por Entidade

#### Cliente (`/cliente`)
- `GET /cliente/buscarTodos` - Lista todos os clientes
- `GET /cliente/buscarPorID/{id}` - Busca cliente por ID
- `POST /cliente` - Cria novo cliente
- `PUT /cliente/{id}` - Atualiza cliente existente
- `DELETE /cliente/{id}` - Remove cliente

#### Produto (`/produto`)
- `GET /produto/buscarTodos` - Lista todos os produtos
- `GET /produto/buscarPorID/{id}` - Busca produto por ID
- `POST /produto` - Cria novo produto
- `PUT /produto/{id}` - Atualiza produto existente
- `DELETE /produto/{id}` - Remove produto

#### Pedido (`/pedido`)
- `GET /pedido/buscarTodos` - Lista todos os pedidos
- `GET /pedido/buscarPorID/{id}` - Busca pedido por ID
- `POST /pedido` - Cria novo pedido
- `PUT /pedido/{id}` - Atualiza pedido existente
- `DELETE /pedido/{id}` - Remove pedido

#### Funcionário (`/funcionario`)
- `GET /funcionario/buscarTodos` - Lista todos os funcionários
- `GET /funcionario/buscarPorID/{id}` - Busca funcionário por ID
- `POST /funcionario` - Cria novo funcionário
- `PUT /funcionario/{id}` - Atualiza funcionário existente
- `DELETE /funcionario/{id}` - Remove funcionário

#### Cargo (`/cargo`)
- `GET /cargo/buscarTodos` - Lista todos os cargos
- `GET /cargo/buscarPorID/{id}` - Busca cargo por ID
- `POST /cargo` - Cria novo cargo
- `PUT /cargo/{id}` - Atualiza cargo existente
- `DELETE /cargo/{id}` - Remove cargo

#### Pagamento (`/pagamento`)
- `GET /pagamento/buscarTodos` - Lista todos os pagamentos
- `GET /pagamento/buscarPorID/{id}` - Busca pagamento por ID
- `POST /pagamento` - Cria novo pagamento
- `PUT /pagamento/{id}` - Atualiza pagamento existente
- `DELETE /pagamento/{id}` - Remove pagamento

#### Tipo de Pagamento (`/tipoPagamento`)
- `GET /tipoPagamento/buscarTodos` - Lista todos os tipos de pagamento
- `GET /tipoPagamento/buscarPorID/{id}` - Busca tipo de pagamento por ID
- `POST /tipoPagamento` - Cria novo tipo de pagamento
- `PUT /tipoPagamento/{id}` - Atualiza tipo de pagamento existente
- `DELETE /tipoPagamento/{id}` - Remove tipo de pagamento

#### Usuário (`/usuario`)
- `GET /usuario/buscarTodos` - Lista todos os usuários
- `GET /usuario/buscarPorID/{id}` - Busca usuário por ID
- `POST /usuario` - Cria novo usuário
- `PUT /usuario/{id}` - Atualiza usuário existente
- `DELETE /usuario/{id}` - Remove usuário

#### Desconto de Fidelidade (`/descontoFidelidade`)
- `GET /descontoFidelidade/buscarTodos` - Lista todos os descontos
- `GET /descontoFidelidade/buscarPorID/{id}` - Busca desconto por ID
- `POST /descontoFidelidade` - Cria novo desconto
- `PUT /descontoFidelidade/{id}` - Atualiza desconto existente
- `DELETE /descontoFidelidade/{id}` - Remove desconto

#### Item do Pedido (`/itemPedido`)
- `GET /itemPedido/buscarTodos` - Lista todos os itens de pedido
- `GET /itemPedido/buscarPorID/{id}` - Busca item de pedido por ID
- `POST /itemPedido` - Cria novo item de pedido
- `PUT /itemPedido/{id}` - Atualiza item de pedido existente
- `DELETE /itemPedido/{id}` - Remove item de pedido

## Configuração e Instalação

### Pré-requisitos
- **Java 17** ou superior
- **Maven 3.6+**
- **MySQL 8.0+** (ou MariaDB compatível)

### Passos para Instalação

1. **Clone o repositório**:
   ```bash
   git clone <url-do-repositorio>
   cd SistemaDeVendas
   ```

2. **Configure o banco de dados**:
   - Instale e inicie o MySQL
   - Crie um banco de dados chamado `sistemadevendas` (ou altere em `application.properties`)
   - Atualize as credenciais no arquivo `src/main/resources/application.properties` se necessário

3. **Compile e execute**:
   ```bash
   mvn clean install
   mvn spring-boot:run
   ```

   Ou usando o wrapper Maven incluído:
   ```bash
   ./mvnw spring-boot:run
   ```

4. **Acesse a aplicação**:
   - API disponível em: `http://localhost:8080`


O projeto inclui testes unitários básicos. Para executar:

```bash
mvn test
```

## Desenvolvimento

### Estrutura de Commits
- Use commits descritivos em português
- Siga o padrão: `tipo: descrição` (ex: `feat: adiciona endpoint de relatório`)

### Branches
- `main`: Branch principal com código estável
- `develop`: Branch de desenvolvimento
- `feature/*`: Branches para novas funcionalidades

## Contribuição

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -am 'Adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

## Licença

Este projeto está sob a licença MIT. Veja o arquivo `LICENSE` para mais detalhes.

## Suporte

Para dúvidas ou problemas, abra uma issue no repositório do GitHub.

---

**Desenvolvido com Spring Boot 3.4.0**