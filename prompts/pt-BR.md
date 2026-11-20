## Auditoria de segurança antes do lançamento

Antes de eu lançar este app, faça uma auditoria de segurança completa do código usando o checklist abaixo.

Para CADA item:
1. Diga o status: ✅ OK, ❌ Problema, ou ➖ Não se aplica (com o motivo).
2. Se houver problema, aponte o arquivo e a linha, explique o risco em uma frase e mostre a correção.
3. Não marque OK sem ter verificado no código. Se não conseguir verificar (ex.: configuração do painel do banco ou da hospedagem), diga o que eu preciso conferir manualmente.

No final, liste os problemas em ordem de gravidade (crítico, alto, médio, baixo) e corrija os críticos primeiro.

### Checklist

**Segredos e credenciais**
1. **Esconder API keys**: nenhuma chave secreta no código do frontend nem no bundle enviado ao navegador; segredos só em variáveis de ambiente do servidor.
2. **Limpar segredos do git**: `.env` e afins no `.gitignore`; procurar chaves já commitadas no histórico e, se houver, revogar e gerar novas (apagar o commit não basta).
3. **Só a chave pública do banco no cliente**: no frontend, apenas a chave pública/anon (ex.: Supabase `anon`); a chave de serviço (`service_role`, admin) nunca sai do servidor.

**Banco de dados e acesso**
4. **Ativar RLS (Row Level Security)**: toda tabela exposta ao cliente com RLS ligado e políticas que limitam cada usuário aos próprios dados.
5. **Criptografia de dados**: dados sensíveis criptografados em repouso e em trânsito; nada sensível em texto puro no banco ou em logs.
6. **Autenticação e autorização no servidor**: toda checagem de "quem é" e "o que pode" feita no backend; o frontend esconder um botão não é proteção.
7. **Restringir acessos**: princípio do menor privilégio em usuários, papéis, chaves, buckets e painéis admin; rotas de admin protegidas.
8. **Bloquear mass assignment**: o servidor aceita só os campos esperados (allowlist); o usuário não consegue enviar `role: "admin"`, `price` ou `user_id` e ter isso salvo.
9. **Proteger cookies**: cookies de sessão com `HttpOnly`, `Secure` e `SameSite`; nada de token de sessão no `localStorage` quando der para evitar.
10. **Hash nas senhas**: senhas com bcrypt, scrypt ou Argon2 (nunca MD5/SHA puro, nunca texto puro); de preferência usar o provedor de auth em vez de implementar.

**Abuso e entrada de dados**
11. **Rate limit**: limite de requisições em login, cadastro, recuperação de senha, envio de e-mail/SMS e endpoints caros (ex.: chamadas de IA).
12. **Proteção contra bots**: CAPTCHA ou equivalente (Turnstile, hCaptcha) em cadastro, login e formulários públicos.
13. **Queries parametrizadas**: nenhuma query montada concatenando texto do usuário; usar parâmetros ou o ORM corretamente (contra SQL injection).
14. **Validação dos inputs**: validar tipo, formato e tamanho de tudo que chega no servidor (ex.: Zod, Joi, Pydantic); sanitizar o que vira HTML (contra XSS).

**Exposição de dados**
15. **Não vazar conteúdo**: mensagens de erro sem stack trace, SQL ou detalhes internos em produção; nenhum dado de um usuário acessível por outro trocando um ID na URL (IDOR).
16. **Restringir uploads**: limitar tipo (checando o conteúdo, não só a extensão), tamanho e quantidade; guardar fora da pasta pública ou com URLs assinadas.
17. **Enxugar respostas da API**: retornar só os campos que a tela usa; nunca mandar hash de senha, e-mails de outros usuários, tokens ou campos internos.

**Transporte e navegador**
18. **Adicionar security headers**: `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options` (ou `frame-ancestors`), `Referrer-Policy`; CORS só para as origens necessárias.
19. **Forçar HTTPS**: redirecionar todo HTTP para HTTPS e ativar HSTS.

**Manutenção**
20. **Dependências e logs**: rodar `npm audit` (ou equivalente) e atualizar pacotes vulneráveis; ter logs de erros e acessos suspeitos, sem gravar senhas, tokens ou dados pessoais neles.
