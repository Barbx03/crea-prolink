# CREA Pro-Link

Plataforma digital de aproximação entre profissionais registrados no Sistema
Confea/Crea, empresas, instituições e contratantes de serviços técnicos em
engenharia, agronomia e geociências.

Protótipo desenvolvido para o **Desafio CREA Pro-Link**, do Conselho Regional
de Engenharia e Agronomia do Amazonas, conforme o Anexo I — Termo de Referência
para Desenvolvimento e Arquitetura de Software.

---

## O problema

Quem precisa de um serviço técnico tem dificuldade de saber quem está
habilitado e tem experiência comprovada para executá-lo. Quem tem essa
experiência tem dificuldade de mostrá-la de forma verificável.

A plataforma resolve os dois lados a partir de uma mesma fonte: os dados
institucionais do CREA-AM. Registro profissional, Anotações de
Responsabilidade Técnica e Certidões de Acervo Técnico entram no sistema
**exclusivamente** pela API oficial do Conselho — não existe caminho para
digitá-los à mão. Sobre essa base verificada, o sistema calcula a
compatibilidade entre demandas e perfis e, mais importante, **explica o
cálculo**: quais competências foram atendidas, quais faltaram, e quanto pesou
cada critério.

---

## Como executar

```bash
cp .env.docker.example .env.docker      # preencha DB_SENHA e o token da API
docker compose --env-file .env.docker up -d --build
```

| Serviço | Endereço |
|---|---|
| Aplicação | http://localhost:8080 |
| Caixa de e-mail de teste | http://localhost:8025 |

Acesso administrativo inicial: `admin@prolink.local` / `Admin@2026` — **troque
no primeiro acesso**.

Instruções completas, incluindo instalação sem Docker: **[`_arq/README.md`](_arq/README.md)**.

---

## Stack

| Camada | Tecnologia |
|---|---|
| Back-end | PHP 8.3, arquitetura MVC própria, repositórios sobre PDO |
| Banco de dados | MariaDB 10.11, `utf8mb4_unicode_ci`, 23 tabelas, 34 chaves estrangeiras |
| Apresentação | Twig, Bootstrap 5, jQuery — servidos localmente, sem CDN |
| Integração | API REST oficial do CREA-AM, com token individual |
| Ambiente | Docker Compose: aplicação, banco e servidor SMTP de teste |

Três dependências diretas: Twig, phpdotenv e PHPMailer.

---

## Requisitos funcionais

| | Requisito | Onde exercitar |
|---|---|---|
| RF01 | Gestão de usuários, perfis e privacidade | `/cadastrar`, `/meu-perfil`, `/privacidade` |
| RF02 | Integração com a API oficial do CREA-AM | `/meu-perfil/registro-crea` |
| RF03 | Portfólio profissional com ARTs e CATs | `/meu-perfil/registro-crea`, `/profissionais/{id}` |
| RF04 | Gestão e compatibilização de demandas | `/demandas`, `/demandas/{id}/correspondencias` |
| RF05 | Manifestação de interesse e comunicação | `/demandas/{id}/manifestar`, `/mensagens` |
| RF06 | Administração da plataforma | `/admin` |
| RF07 | Notificações por e-mail configuráveis | `/notificacoes`, `/admin/configuracoes` |

O rastreamento item a item, incluindo requisitos não funcionais e técnicos,
está em **[`_arq/REQUISITOS.md`](_arq/REQUISITOS.md)**.

---

## Três decisões que definem a solução

**A origem do dado importa mais que o dado.** A plataforma distingue, em todo
lugar, o que o profissional declara do que a base oficial confirma. Uma
experiência vinculada a uma ART validada aparece como *comprovada*; sem
vínculo, como *declarada*. O selo de registro no CREA-AM não pode ser concedido
nem pelo administrador: depende de retorno positivo da API.

**Ordenação sem explicação não serve.** O cálculo de aderência devolve, junto
da nota, a lista de critérios com peso declarado, percentual obtido e
justificativa em linguagem comum. Requisitos eliminatórios aparecem como
impedimento explícito, nunca como exclusão silenciosa.

**Privacidade como padrão, não como opção.** Contato, documento e valor-hora
ficam ocultos até que o titular autorize. Cada item do portfólio tem
visibilidade individual. Consentimento é separado por finalidade, revogável, com
histórico imutável e efeito imediato.

---

## Documentação

| Documento | Conteúdo |
|---|---|
| [`_arq/README.md`](_arq/README.md) | instalação, configuração, execução, atualização e diagnóstico |
| [`_arq/ARQUITETURA.md`](_arq/ARQUITETURA.md) | camadas, componentes, regras de negócio e limites conhecidos |
| [`_arq/ESTRUTURA-DIRETORIOS.md`](_arq/ESTRUTURA-DIRETORIOS.md) | finalidade de cada pasta e convenções de nomenclatura |
| [`_arq/DEPENDENCIAS.md`](_arq/DEPENDENCIAS.md) | bibliotecas, versões e licenças |
| [`_arq/REQUISITOS.md`](_arq/REQUISITOS.md) | rastreamento dos requisitos até o código |
| [`_arq/SEGURANCA.md`](_arq/SEGURANCA.md) | controles de segurança e tratamento de dados pessoais |
| [`_arq/mer/`](_arq/mer/) | Modelo Entidade-Relacionamento em SVG, PNG e PDF |
| [`_arq/estrutura.sql`](_arq/estrutura.sql) | criação do banco, tabelas, índices e relacionamentos |

---

## Licença

Código sob licença MIT. As bibliotecas de terceiros mantêm suas licenças
originais, todas compatíveis com uso institucional — relação em
[`_arq/DEPENDENCIAS.md`](_arq/DEPENDENCIAS.md).
