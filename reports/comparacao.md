# Relatório de Comparação — Modelos `chamados-r1` vs. `chamados-r2`

## 1. Contexto e Alteração nos Dados
- **Snapshot inicial (`dados-v1`, commit `93de19e`):** contém erro intencional de rotulagem no chamado `006` (`bloqueio de acesso` marcado como `comum`, contrariando a regra de que bloqueios são `urgente`).
- **Snapshot corrigido (`dados-v2`, commit `4b69a2b`):** corrige a classe do chamado `006` para `urgente`, mantendo a partição de treino/teste fixa.

## 2. Rastreabilidade de Artefatos e Hashes

| Componente | Versão 1 (`dados-v1` / `modelo-r1`) | Versão 2 (`dados-v2` / `modelo-r2`) | Status |
| :--- | :--- | :--- | :--- |
| **Commit / Tag** | `93de19e` (`dados-v1`) → `9c463d4` (`modelo-r1`) | `4b69a2b` (`dados-v2`) → `2cf74af` (`modelo-r2`) | Integrado via PR `#1` (`223d563`) |
| **Dataset (`dataset_id`)** | `chamados-v1` | `chamados-v2` | Atualizado |
| **MD5 DVC (`chamados.csv.dvc`)** | `0c11150657324c928472c835a49492b6` (321 B) | `bd856dc82f2847a61b46c44c5da8d6ab` (323 B) | Atualizado |
| **SHA-256 Dataset** | `bc3fd86a3a5f598841a58839a9726d901fa7e8361ffe79c5b43873ad8a1328ac` | `4ca2e29cb930a9161c2a851b3c71f5529dbb812db607ee4a5ea95d2223baa14d` | Atualizado |
| **SHA-256 Partição (`split.csv`)** | `2ef820171c1a4625fe216e6da67c7c8dcf9d97a08288c0239cd23f1a6961d981` | `2ef820171c1a4625fe216e6da67c7c8dcf9d97a08288c0239cd23f1a6961d981` | **Inalterado** |
| **SHA-256 Modelo (`model.json`)** | `09d965544c9298c5586af0a1becbf07fb3f3c7b7c228a803ab91179f8b458a09` | `3bcddff15fe272fe16e092dfb76dab30fa3786cd8c2ce4849ccf5ace002c6ee6` | Atualizado |
| **`run_id` de Execução** | `7b6cfdc0-af09-448a-81a3-2b36599d3564` | `5b268982-bfd5-4ace-abba-8a01b319a2b6` | Atualizado |

## 3. Comparativo de Métricas na Partição de Teste Fixa

| Métrica | `chamados-r1` (`dados-v1`) | `chamados-r2` (`dados-v2`) | Variação |
| :--- | :---: | :---: | :---: |
| **Acurácia global** | `0.75` | `1.00` | `+0.25` |
| **Recall — `comum` (suporte: 2)** | `1.00` | `1.00` | `0.00` |
| **Recall — `urgente` (suporte: 2)** | `0.50` | `1.00` | `+0.50` |

## 4. Conclusão Técnica e Reprodutibilidade
- No modelo `chamados-r1`, o token `"bloqueio"` foi mapeado como `"comum"` devido ao empate/ruído gerado pelo rótulo incorreto no ID `006`, reduzindo o *recall* da classe `urgente` para `0.50`.
- Após a correção em `dados-v2` (mantendo `split_sha256` idêntico para garantir comparabilidade justa), o token `"bloqueio"` passou a ser classificado como `"urgente"`, elevando a acurácia global para `1.00`.
- A restauração em clone limpo (`aula-ml-restauracao`) via `git checkout` + `dvc pull` recuperou fielmente tanto o estado `modelo-r1` quanto o estado `modelo-r2`.
