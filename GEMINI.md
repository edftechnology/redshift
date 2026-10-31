# GEMINI - Guia Mestre

## Qualidade científica

Priorize qualidade, rigor científico, reprodutibilidade e auditabilidade; não produtividade ou velocidade de implementação.


Este arquivo serve como índice central para as instruções específicas do Gemini.  
Cada arquivo dedicado **não é mesclado** aqui, para permitir edição independente.

---

## Padrões de código Python

- Aplicar a [PEP 8](https://peps.python.org/pep-0008/) a todo código Python criado ou alterado.
- Escrever docstrings de módulos, classes e funções públicas conforme a [PEP 257](https://peps.python.org/pep-0257/).
- Manter nomes de identificadores em inglês dos Estados Unidos, conforme a regra geral do projeto.
- Aplicar essas PEPs somente a código Python e às docstrings Python. Elas não definem o estilo da prosa em Markdown nem de comandos de shell, arquivos JSON, código C ou outros formatos.

## Formato de referências bibliográficas

- Formatar cada referência no padrão ABNT, distribuindo a entrada em linhas separadas: identificação e autoria; título em negrito; link precedido por `Disponível em:`; identificação da fonte; data de acesso.
- Escrever somente a primeira letra do título em maiúscula, respeitando siglas e nomes próprios.
- Não reunir os elementos da referência em uma única linha.

Exemplo:

```text
[1] OPENAI.
**Instalar o `mousetrail` no `linux ubuntu` pelo `terminal emulator`**.
Disponível em: <https://chatgpt.com/g/g-p-6980caf949648191ad6acfcdbe590f9e-instalar/c/6ac754e0-d664-83ea-906a-50fab09b9f73>.
ChatGPT.
Acessado em: 08/10/2026.
```

## Estrutura

- `scripts/gemini_git.md` → Instruções e fluxos de trabalho para Git, GitHub e GitLab
- `scripts/gemini_github_actions.md` → Instruções para GitHub Actions
- `scripts/gemini_latex.md` → Instruções e padrões para documentos LaTeX
- `scripts/gemini_python.md` → Instruções para Python, PEP8, Sphinx e formatação de código
- `scripts/gemini_shell.md` → Instruções para Shell Script e terminal

---

## Como usar no Gemini

No **Gemini CLI** (ou outra instância do Gemini), você pode pedir para o modelo considerar **somente** uma seção ou arquivo específico, por exemplo:

> "Use apenas as instruções do arquivo `scripts/gemini_python.md`"  
> "Considere as instruções do `scripts/gemini_git.md` para revisar este commit"

---

## Leitura para Instruções Externas

- [gemini_git.md](scripts/gemini_git.md)
- [gemini_github_actions.md](scripts/gemini_github_actions.md)
- [gemini_latex.md](scripts/gemini_latex.md)
- [gemini_python.md](scripts/gemini_python.md)
- [gemini_shell.md](scripts/gemini_shell.md)

---

> **Nota:** Cada arquivo é independente e pode ser atualizado separadamente.  
> O `GEMINI.md` serve apenas como guia/índice mestre para o Gemini.

## Segurança e auditoria de código

### Diretrizes gerais

- Priorizar a segurança, a integridade e a confidencialidade do código-fonte.
- Verificar possíveis vulnerabilidades antes de concluir alterações relevantes.
- Utilizar o plugin `codex-security` quando disponível e apropriado.
- Nunca expor senhas, tokens, chaves de API ou outras credenciais.
- Não executar comandos destrutivos sem autorização explícita.
- Não modificar arquivos durante auditorias solicitadas como somente leitura.

### Auditoria de segurança

Quando o usuário solicitar uma auditoria de segurança, verificar:

1. Vulnerabilidades no código Python e Shell Script.
2. Exposição de senhas, tokens e chaves de API.
3. Dependências com vulnerabilidades conhecidas.
4. Comandos perigosos ou destrutivos.
5. Riscos de injeção de comandos e execução arbitrária.
6. Configurações inseguras e permissões excessivas.
7. Possíveis problemas de segurança nos submódulos Git.

### Relatório de segurança

Apresentar os resultados contendo:

- Severidade: crítica, alta, média, baixa ou informativa.
- Arquivo e linha afetados.
- Descrição da vulnerabilidade e evidências.
- Impacto potencial.
- Recomendação de correção.
- Ferramentas efetivamente utilizadas.
- Limitações da auditoria e verificações não realizadas.

Não afirmar que o projeto está completamente seguro apenas porque nenhuma vulnerabilidade foi identificada.

### Eficiência de tokens e contexto

- Evitar auditorias completas quando a alteração for pequena e localizada.
- Priorizar a análise dos arquivos modificados.
- Reutilizar resultados válidos de verificações anteriores quando apropriado.
- Evitar leituras repetidas e desnecessárias.
- Executar auditorias completas quando explicitamente solicitado ou quando houver mudanças relevantes para a segurança.

## Gestão de risco de modelos (MRM)

Aplique estas orientações quando o sistema usar métodos quantitativos, estatísticos, de aprendizado de máquina ou abordagens semelhantes para produzir estimativas, classificações, recomendações ou decisões. Ajuste o rigor à finalidade, materialidade, exposição, complexidade e contexto de uso; não imponha controles desnecessários a ferramentas sem risco de modelo relevante.

- Registre, conforme o risco, finalidade e uso previsto, responsável, versão, dados e linhagem, premissas, limites de uso, limitações conhecidas, dependências, critérios de desempenho, revisões e aprovações.
- Antes de uso material, avalie a adequação conceitual, os dados, a implementação e os resultados. Use testes reproduzíveis, comparações ou referências independentes quando apropriado; busque crítica objetiva por pessoa qualificada que não seja a única autora ou responsável pelo modelo.
- Monitore desempenho, qualidade e mudanças nos dados e no contexto. Defina sinais de alerta, resposta a desvios, revisão de mudanças e critérios para limitar, recalibrar, revalidar, suspender ou retirar o modelo.
- Comunique incerteza, premissas, limitações e restrições de uso. Não prometa precisão, imparcialidade, validação ou conformidade sem evidência; mantenha supervisão humana adequada ao impacto da decisão.
- Preserve documentação e evidências suficientes para reproduzir e auditar decisões, testes e mudanças. Proteja dados pessoais, confidenciais e credenciais; não os inclua em logs, prompts, commits ou artefatos sem autorização e necessidade.
- Considere interdependências, modelos de terceiros e métodos determinísticos de impacto material. Aplique controles equivalentes quando o risco justificar, mesmo que a ferramenta não seja formalmente classificada como modelo.
- Consulte `docs/references/README.md` quando disponível, confirme a edição e a jurisdição aplicáveis e trate os materiais como orientação contextual, não como obrigação legal universal. A orientação interagências dos EUA de 2026 não abrange modelos generativos/agênticos; avalie controles adequados ao contexto. Verifique também datas de vigência antes de afirmar aplicabilidade.
