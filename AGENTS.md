# Desktop Buddy Robot — Guia do Projeto

## Contexto

Robô de mesa em formato de cabeça quadrada, projetado para acomodar Raspberry Pi 3 Model B, tela OLED frontal e powerbank removível. Modelagem em Blender, impressão 3D em PLA.

## Estado Atual

- A fonte principal do projeto atual e este arquivo.
- O estado corrente do hardware/casca e o redesign em caixa salvo em `redesign/box_redesign_mcp_v1.blend`.
- A arquitetura antiga de 8 pecas virou referencia legada e nao deve ser tratada como o estado atual do produto.
- A imagem `montagem.png` ja reflete o redesign atual.
- A revisao de primeiro print do redesign esta em `redesign/PRINT_REVIEW_V1.md`.

## Hardware Real (confirmado)

| Componente | Especificação |
|---|---|
| Tela | **2.42" OLED I2C** (modelo 2.420LED-IIC VER:1.1), SSD1309, 128×64px |
| Tela referência pública | Waveshare **2.42inch OLED Module** (usar apenas como referência de envelope/display: SSD1309, 61.5 × 39.5 mm, área visível 55.01 × 27.49 mm) |
| Tela PCB | 61.5 × 39.5 mm |
| Tela área visível | 55.01 × 27.49 mm |
| Tela pixel | 0.4 × 0.4 mm |
| Tela interface | I2C (4 pinos: GND, VDD, SCL, SDA) |
| Tela furos reais | 4 furos circulares nos cantos da PCB da placa 2.420LED-IIC VER:1.1 |
| Tela fixação | Não usar pinos de encaixe como premissa. Para a placa real, usar a própria PCB 2.420LED-IIC VER:1.1 como referência de furação; referência pública equivalente não deve sobrescrever a geometria real dos furos |
| Camera PCB | 25.0 x 24.0 mm, espessura ~1.0 mm |
| Camera furos | 4 furos, diametro ~2.2 mm, espacamento 21.0 x 21.0 mm, offset ~2.0 mm da borda |
| Raspberry Pi | 3 Model B — 85 × 56 mm, furos M2.5 a 58 × 49 mm |
| Impressora | Creality Ender 3 V3 SE |
| Material | PLA |
| Slicer | Creality Print |

## Blender — Configuração e Convenções

- **Versão**: Blender 5.1+
- **Unidades**: METRIC, `scale_length=0.001`, `length_unit=MILLIMETERS`
- **Modelagem interna**: em metros (Blender default), exibição em mm
- **Export STL**: usar `bpy.ops.wm.stl_export` (Blender 5.x API, **não** `bpy.ops.export_mesh.stl`)
- **Scale no export**: `global_scale=1000.0` (metros → mm)
- **Format**: `ascii_format=False`, `export_selected_objects=True`
- **Boolean solver**: sempre usar `EXACT` (evita artefatos e meshes quebrados)
- **Limpeza pós-boolean**: `remove_doubles(threshold=0.0001)` + `normals_make_consistent`

## Impressão 3D — Lições Aprendidas

### Tolerâncias

- **0.3mm é muito justo** para PLA no Ender 3. Peças não encaixam ou encaixam com muita força
- **0.5–0.7mm funciona** para encaixes por pressão em PLA
- Considerar `elephant's foot` na primeira camada — pode adicionar 0.1-0.2mm extras
- Layer height 0.20mm não é causa de aperto; o problema é a folga no modelo

### Orientação

- Peças com features finas (lips, standoffs) devem ser modeladas **flat** (base no print bed, features para cima)
- Isso elimina necessidade de suportes e melhora a qualidade
- Suportes, retentores e tampas pequenas devem seguir essa regra por padrão no redesign atual

### Mesh quality

- Booleans com solver `FAST` geraram peças "quebradas" no slicer
- Sempre usar solver `EXACT` + merge doubles + recalcular normais

## Arquitetura Atual (Redesign V1)

- Envelope externo principal: **150 x 95 x 120 mm**
- Shell frontal com abertura integrada da OLED e abertura da camera no canto superior esquerdo
- Fixacao da camera integrada na shell, guiada pelas medidas reais da PCB da camera
- Tampa traseira simples de encaixe, sem chassi interno denso
- 3 pecas internas minimas apoiadas em ledges internos: retentor OLED, suporte Raspberry e power shelf
- Saida lateral direita para alimentacao do Raspberry
- Prioridade atual: montagem clara, espaco interno, passagem de fios e manutencao facil

## Peças Atuais do Projeto (5 total)

| # | Arquivo | Dimensões | Notas |
|---|---|---|---|
| 01 | `rd_shell_v1.stl` | 150x95x120mm | Shell principal. Abertura OLED integrada, abertura da camera no canto superior esquerdo, posts frontais, ledges internos e saida lateral de energia |
| 02 | `rd_back_cover_v1.stl` | 149x13x119mm | Tampa traseira com plug. Folga nominal de 0.6mm por lado e chanfro de entrada de 0.6mm |
| 03 | `rd_oled_retainer_v1.stl` | 74x2.4x50mm | Retentor traseiro da OLED alinhado aos 4 posts frontais. Furos tratados como piloto no primeiro print |
| 04 | `rd_rpi_support_v1.stl` | 135x68x8.5mm | Suporte simples do Raspberry apoiado em ledges internos, com area aberta para cabos |
| 05 | `rd_power_shelf_v1.stl` | 136x68x10.5mm | Prateleira aberta para powerbank, com passagem livre de fios |

## Revisao de Primeiro Print (V1)

- A abertura interna traseira da shell esta em **144.0 x 114.0 mm**
- O plug da tampa esta em **142.8 x 112.8 mm**
- Folga nominal do plug: **0.6 mm por lado**
- Foi adicionado um chanfro de entrada de **0.6 mm** para reduzir travamento por elephant foot
- Se houver aperto no primeiro print, o ajuste seguinte deve ser pequeno: **+0.2 mm total no plug**
- Na camera, tratar os furos pequenos como **furos piloto** ate validar o comportamento real do print
- Na OLED, a referencia final de furação e a placa real **2.420LED-IIC VER:1.1** confirmada por foto, com **4 furos circulares nos cantos**

## Referencia Legada

- A arquitetura anterior de 8 pecas (shell, internal chassis, raspberry mount, screen mount, powerbank holder, back cover, cable guides e tolerance test) fica apenas como historico
- Novas iteracoes mecanicas devem partir do redesign atual, nao do conjunto antigo

## Erros Já Cometidos (não repetir)

1. **Modelar com dimensões de tela errada** — usamos specs de 3.5" SPI quando a tela real era 2.42" OLED. Sempre confirmar hardware real antes de modelar
2. **Tolerância de 0.3mm para encaixes PLA** — muito justo. Usar 0.5–0.7mm
3. **Boolean solver FAST** — gera meshes com buracos. Usar EXACT
4. **Modelar peças na vertical** — gera necessidade de suportes e qualidade ruim. Modelar flat
5. **Usar `bpy.ops.export_mesh.stl`** — deprecated no Blender 5.x. Usar `bpy.ops.wm.stl_export`

## Saída

- Redesign atual: STLs em `/redesign/stl/`
- Checkpoint Blender atual: `/redesign/box_redesign_mcp_v1.blend`
- Imagem de montagem atual: `/montagem.png`
- Arquivos em `/stl/` sao legado de slicing/impressao anterior e nao o output principal do redesign atual
- Export usar `global_scale=1000.0` para gerar valores em mm no arquivo

## Filosofia

> Primeiro funcional. Depois bonito. Sempre simples.
