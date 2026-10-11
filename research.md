# Заметки исследования (2026-10)

Помечено «(не проверено)», если не удалось подтвердить первоисточником.

## Похожие работы

### Minecraft

- **World2Minecraft / MinecraftOcc** — arXiv [2604.27578](https://arxiv.org/abs/2604.27578), апр. 2026.
  - Датасет: 100,165 скринов, 156 сцен.
  - Позы камеры получены модом, воксельный эталон — через WorldEdit, 1452 класса блоков.
  - IoU/mIoU: MonoScene 40.7/20.9, Symphonies 39.1/21.6, NDC-Scene 37.1/17.8, ISO 33.8/14.8.
  - Код и данные обещаны на world2minecraft.github.io (не проверено).
- **3D-Craft / VoxelCNN** (FAIR, ICCV'19, CC-BY-NC): 2500 домов с порядком постройки.
- **Dream-Cubed** — arXiv 2604.22847: огромный корпус блоков, 3D-диффузия. Ссылки и лицензия не проверены.
- **HF Minecraft-Schematics** (Hack337): 669 построек .litematic/.schem.
- **HF minecraft-schematic-generator** (MIT): трансформер 79M.
- **IGLU 2022** (RL-строительство): лучший агент F1≈0.25 — аргумент против чистого RL.
- **PLAD** (CVPR'22): псевдо-разметка лучше policy-gradient RL для вывода «программ формы».

### Монокулярная semantic scene completion (RGB → 3D-воксели)

| Модель | Лицензия | Заметка |
|---|---|---|
| MonoScene | Apache-2.0 | самая простая, FLoSP + 3D UNet |
| ISO | Apache-2.0 | MonoScene + ветка глубины, хороший кандидат |
| Symphonies | MIT | лучший mIoU на MinecraftOcc |
| OccFormer | Apache-2.0 | под улицы, outdoor |
| VoxFormer | NVIDIA NC | только некоммерческое использование |

### Глубина, нормали, камера

- **MoGe-2** (MIT): point map, нормали и FOV в одном проходе — лучший кандидат на дообучение.
- **Depth Anything V2**: Small под Apache, остальные CC-BY-NC.
- **GeoCalib** (Apache): вертикальный FOV и гравитация.
- **DeepLSD / NeurVPS** (MIT): линии и точки схода.
- **Калибровка Manhattan-мира:** две ортогональные точки схода дают f и R. Решётка блоков даёт масштаб.

## Инструменты

### .litematic

- **Формат:** gzip NBT. Регионы, `BlockStatePalette` (индекс 0 = воздух), `BlockStates` — long[] с упаковкой, где записи **могут переходить через границу long** (в отличие от ванильных чанков).
- **Библиотеки:**
  - `litemapy` — GPL-3, на Python, просто;
  - `nucleation` — MIT, Rust + Python, быстро, умеет ещё .schem и импорт миров;
  - `nbtlib` — MIT.

### Чтение миров

- **numpy-декодер Anvil:** zlib → NBT → распаковка `block_states.data`. Кешировать в .npz.
- `anvil-parser2` и `mcworldlib` медленные на поблоковом доступе.
- **Amulet** стал платным — избегать.

### Рендер и захват

- **Настоящий клиент — основной источник.**
  - Fabric client gametest API (`takeScreenshot`, `waitForChunksRender`), работает под Xvfb.
  - Попиксельная разметка: `world.raycast(..., ShapeType.OUTLINE, FluidHandling.ANY)`.
  - Формы блоков: `getOutlineShape(...).getBoundingBoxes()`.
- **Синтетика:**
  - deepslate (MIT, WebGL);
  - prismarine-viewer (MIT, three.js, headless);
  - свой moderngl-рендерер — самый быстрый.

### FOV в Minecraft Java

- Вертикальный, по умолчанию 70, диапазон 30–110.
- Горизонтальный FOV = `2·atan(tan(v/2)·aspect)`. При 70 и 16:9 это ≈102°.
- Реальный FOV = настройка × модификатор (спринт ~1.15, полёт 1.1, лук 0.85, подзорная труба 0.1), умноженный на «FOV Effects».
- В воде дополнительно ×6/7.

## Миры PepeLand (официально: pepeland.net/about/seasons/N/worlds)

| Сезон | Версия | Миры построек (архив) |
|---|---|---|
| S7 | 1.19.2 | 2 + 3 ГБ |
| S8 | 1.20.1 | 9 + 4.5 ГБ |
| S9 | 1.21.3 | 4 + 8 ГБ |
| S10 | 1.21.8 | 3.0 ГБ + 6.5 ГБ (мир построек 2 + бледный + арты) |

- Лицензии на миры нет. По правилам сайта контент принадлежит авторам: для публикации датасета или модели нужно разрешение.
- У сервера есть свой ресурспак — использовать при генерации данных.
