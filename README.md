# Sprint 4 – Gestão de Usuários com Autenticação e Relatórios

## Tecnologias
- TypeScript  
- NestJS  
- Postgres  
- Prisma  
- Docker  

---

## Objetivo
Implementar um sistema de gerenciamento de usuários com autenticação baseada em JWT, controle de permissões por nível e geração de relatórios em CSV e PDF.

---

## Modelo de Usuário (User)
- `id`: UUID ou Int 
- `name`: string  
- `email`: string (único, válido)  
- `password`: string (armazenada com hash e salt)  
- `level`: número inteiro entre 1 e 5  
- `createdAt`: timestamps automáticos  

## Modelo de Sala (Room)
- `id`: UUID ou Int
- `name`: String
- `request_level` Int (Nível necessário para ingressar naquela sala)
- `is_blocked` Boolean

---

# DESAFIOS EXTRAS

- Caso o usuário logado tente entrar numa sala que ele nao tenha autorização, mande um email para 
todos os usuários níveis 5, de quando e qual sala o usuário tentou entrar.
Dica, não use seu email para testar, use o https://www.fakemail.net/

## Funcionalidades Recomendadas/Mínimas

### 1. CRUD de Usuários

✅ **Criar Usuário**  
- Rota: `POST /users`  
- Requer: nome, email, senha, nível  
- Senha deve ser hasheada (bcrypt ou argon2)

✅ **Listar Todos os Usuários**  
- Rota: `GET /users`  
- Acesso: qualquer usuário autenticado

✅ **Buscar Usuário por ID**  
- Rota: `GET /users/:id`  
- Acesso: qualquer usuário autenticado

✅ **Atualizar Usuário**  
- Rota: `PATCH /users/:id`  
- Acesso:  
- O próprio usuário pode atualizar seus dados, porém não pode alterar seu nível  
- Nível 5 podem atualizar qualquer usuário e seus níveis de acesso

✅ **Deletar Usuário**  
- Rota: `DELETE /users/:id`  
- Acesso: somente usuários com nível 4 ou 5

---

### 2. Controle de Nível

✅ **Atualizar Nível de Acesso**  
- Rota: `PATCH /users/:id/level`  
- Requer: novo nível (1 a 5)  
- Acesso: somente usuários com nível 5

---

### 3. Autenticação e Segurança

✅ **Login**  
- Rota: `POST /auth/login`  
- Entrada: email e senha  
- Saída: token JWT

✅ **Middleware de Autenticação**  
- Rota protegida deve verificar token JWT no header Authorization

✅ **Middleware de Autorização**  
- Verifica nível do usuário para permitir operações sensíveis

---

### 4. CRUD de Salas
- **Criação da Sala**
- Rota: `POST /room`
- Entrada: name e request_level
- Saída: 200 OK

- **Edição de Sala**
- Rota: PUT `/room/{id}`
- Entrada: name (opcional), request_level (opcional) - Lembre-se, somente nível 5 pode alterar a sala!
- Saída: 200 OK (sucesso) ou 404 Not Found (sala não existe)

**Listar Todas as Salas**
- Rota: GET /rooms
- Saída: Lista de objetos { id: string, name: string, request_level: number } com status 200 OK.

**Buscar Sala por ID**
Rota: `GET /room/{id}`
Saída: Objeto { id: string, name: string, request_level: number } com status 200 OK ou 404 Not Found.

**Bloquear Sala e Desbloquear Sala**
Rota: `PATCH /room/{id}`
Saída: 200 OK (sucesso) ou 404 Not Found (sala não existe).

---

## 🔒 Controle Obrigatório por `level` (Role)

- Toda rota que exige autorização **deve obrigatoriamente verificar o `level` do usuário**.
- Essa verificação deve ser feita usando um **guard** ou **middleware** centralizado.
- O campo `level` representa a "role" do usuário e deve estar presente em todos os usuários autenticados.
- Níveis de acesso:
  - `1` = Usuário comum
  - `2` a `4` = Níveis intermediários
  - `5` = Administrador
- Rota sensível **não deve ser acessada** sem validação explícita do nível.
- A ausência ou manipulação do `level` deve resultar em erro 403 (Forbidden).
- Todas as rotas devem ser feitas por usuários logados!

## Funcionalidades Opcionais

### 5. Geração de Relatórios

✅ **Relatório de Usuários**  
- Rota: `GET /report`  
- Acesso: qualquer usuário autenticado  
- Saída: permite escolher entre CSV e PDF

---

### 5. Simulação de Entrada em Sala

✅ **Tentar Entrar em uma Sala**  
- Rota: `POST /room/enter`  
- Entrada:  
  - `userId`: ID do usuário  
  - `roomCode`: código da sala  
- Regras:  
  - Apenas usuários autenticados podem acessar  
  - Caso o usuário com um nível abaixo tente acessar, 
  O erro deve ser o 403 (Forbidden)
- Saída:  
  - Sucesso: mensagem de confirmação de entrada  
  - Erro: motivo da recusa (ex: "Nível insuficiente", "Sala inexistente")

    Serviço Opcional - Fazer um Log de acessos aquela sala todas as vezes que alguem tentar acessar!
    (Isso não é uma rota e sim um serivço dentro da sua aplicação - Dica: Faça uma tabela nova no banco de dados para registrar)

  ### 6. Crachá
  
  **

## Backlog Técnico

### 📌 Configuração Inicial
- [ ] Estrutura do projeto NestJS  
- [ ] Configuração do Prisma + Postgres  
- [ ] Docker para ambiente local  
- [ ] Modelagem da entidade `User`
- [ ] Modelagem da entidade `Room` 
- [ ] Hash de senha (bcrypt recomendado)

### 📌 CRUD de Usuário
- [ ] Rotas, serviços e repositórios  
- [ ] Autorização por nível  
- [ ] Validações (class-validator): email válido, senha mínima, nível entre 1-5

### 📌 CRUD de Room
- [ ] Rotas, serviços e repositórios  

### 📌 Autenticação
- [ ] Endpoint de login  
- [ ] Geração de JWT  
- [ ] Middleware para verificar token  
- [ ] Middleware de autorização por nível

### 📌 Relatórios
- [ ] Geração de CSV (ex: `csv-writer`, `fast-csv`)  
- [ ] Geração de PDF (ex: `pdfkit`, `puppeteer`)  
- [ ] Endpoint `/report`

### 📌 Testes e Documentação (Opcional)
- [ ] Testes unitários com Jest  
- [ ] Documentação com Swagger

---

## Definition of Done (DoD)
- Todas as rotas implementadas e funcionando  
- Regras de autenticação e autorização aplicadas corretamente  
- Senhas seguras   
- Testes e documentação se possível
