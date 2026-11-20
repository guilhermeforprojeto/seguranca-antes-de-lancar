# Checklist de segurança antes do deploy

Copie para a descrição do PR ou para uma issue e marque item por item.

### Segredos e credenciais
- [ ] 1. Nenhuma API key secreta no frontend ou no bundle
- [ ] 2. `.env` no `.gitignore` e histórico do git sem segredos (chaves vazadas revogadas)
- [ ] 3. Só a chave pública/anon do banco no cliente; `service_role` apenas no servidor

### Banco de dados e acesso
- [ ] 4. RLS ativado em todas as tabelas expostas, com políticas por usuário
- [ ] 5. Dados sensíveis criptografados em repouso e em trânsito
- [ ] 6. Autenticação e autorização checadas no servidor
- [ ] 7. Menor privilégio em papéis, chaves, buckets e rotas de admin
- [ ] 8. Mass assignment bloqueado (allowlist de campos)
- [ ] 9. Cookies de sessão com `HttpOnly`, `Secure` e `SameSite`
- [ ] 10. Senhas com bcrypt, scrypt ou Argon2

### Abuso e entrada de dados
- [ ] 11. Rate limit em login, cadastro, reset de senha e endpoints caros
- [ ] 12. Proteção contra bots nos formulários públicos
- [ ] 13. Queries parametrizadas (sem concatenar input do usuário)
- [ ] 14. Validação de todos os inputs no servidor e sanitização de HTML

### Exposição de dados
- [ ] 15. Erros sem detalhes internos em produção; sem IDOR
- [ ] 16. Uploads restritos por tipo real, tamanho e local de armazenamento
- [ ] 17. Respostas da API só com os campos necessários

### Transporte e navegador
- [ ] 18. Security headers (CSP, HSTS, nosniff, frame-ancestors, Referrer-Policy) e CORS restrito
- [ ] 19. HTTPS forçado com redirecionamento e HSTS

### Manutenção
- [ ] 20. Dependências auditadas e atualizadas; logs sem dados sensíveis
