# Desktop Buddy Robot — Guia do Projeto

## Contexto

Robô de mesa em formato de cabeça quadrada, projetado para acomodar Raspberry Pi 3 Model B, tela OLED frontal e powerbank removível. Modelagem em Blender, impressão 3D em PLA.

## Hardware Real (confirmado)

| Componente | Especificação |
|---|---|
| Tela | **2.42" OLED I2C** (modelo 2.420LED-IIC VER:1.1), SSD1309, 128×64px |
| Tela PCB | 61.5 × 39.5 mm |
| Tela área visível | 55.01 × 27.49 mm |
| Tela pixel | 0.4 × 0.4 mm |
| Tela interface | I2C (4 pinos: GND, VDD, SCL, SDA) |
| Tela furos | Ovais/rasgados nos cantos — **não servem para pinos de encaixe** |
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
- Peças 03 e 04 foram refeitas flat após ficarem ruins com suportes

### Mesh quality

- Booleans com solver `FAST` geraram peças "quebradas" no slicer
- Sempre usar solver `EXACT` + merge doubles + recalcular normais

## Peças do Projeto (8 total)

| # | Arquivo | Dimensões | Notas |
|---|---|---|---|
| 01 | `01_shell.stl` | 120×80×100mm | Abertura tela: **57×29.5mm** (para 2.42" OLED). Bevel 5mm nos cantos verticais, 5 slots ventilação/lado, 4 posts internos |
| 02 | `02_internal_chassis.stl` | 114×74×96.5mm | Zona tela: **12mm** profundidade. Divisores, side rails, shelf RPi a 20mm, cable passages 20×12mm, screen rails a 40mm |
| 03 | `03_raspberry_mount.stl` | 89×60×10mm | Flat. 4 standoffs M2.5 (58×49mm), base 2mm, EXACT boolean |
| 04 | `04_screen_mount.stl` | 69.2×47.2×3mm | Flat. Pocket PCB **62.2×40.2mm** (0.7mm tolerância). Lips 1.5mm thick, laterais parciais 60%. Janela 57×29.5mm. Slot cabo I2C 18mm. 4 posts embaixo para chassis |
| 05 | `05_powerbank_holder.stl` | 105.4×66.4×20mm | U-shaped, retention bumps, USB passage |
| 06 | `06_back_cover.stl` | 116.2×8×98.7mm | Snap-fit clips, ventilação, M3 opcional |
| 07 | `07_cable_guides.stl` | 90×19.5×36.5mm | U-channel, riser, clips, tabs M2.5 |
| 08 | `08_tolerance_test.stl` | 60×40×21mm | Pinos/furos com folgas 0.2–0.5mm |

## Design da Peça 04 (Screen Mount)

A tela 2.42" OLED encaixa por **pressão nas lips**, não por pinos nos furos (furos da PCB são ovais/rasgados). Os 4 posts cilíndricos na parte de baixo do frame servem para **fixar ao chassis interno**, não para a PCB.

Slot para cabo no lip superior (onde ficam os pinos GND/VDD/SCL/SDA).

## Erros Já Cometidos (não repetir)

1. **Modelar com dimensões de tela errada** — usamos specs de 3.5" SPI quando a tela real era 2.42" OLED. Sempre confirmar hardware real antes de modelar
2. **Tolerância de 0.3mm para encaixes PLA** — muito justo. Usar 0.5–0.7mm
3. **Boolean solver FAST** — gera meshes com buracos. Usar EXACT
4. **Modelar peças na vertical** — gera necessidade de suportes e qualidade ruim. Modelar flat
5. **Usar `bpy.ops.export_mesh.stl`** — deprecated no Blender 5.x. Usar `bpy.ops.wm.stl_export`

## Saída

Todos os STLs ficam em `/stl/`. Export com `global_scale=1000.0` para gerar valores em mm no arquivo.

## Filosofia

> Primeiro funcional. Depois bonito. Sempre simples.
