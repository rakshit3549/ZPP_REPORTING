# 📘 ABAP Report: YPP_I030_ZP60_MAPEX

## 🔖 1. Overview

This report is designed to extract **MAPEX-relevant data** from production/process orders, filter it using user-defined criteria, and present the results in one of two ways:
- **ALV Grid Display** – interactive visualization in SAP GUI  
- **File Export** – download to local or application server in CSV format  

### ✨ Key Features
- Flexible selection screen  
- Dynamic data extraction  
- ALV grid display (CL_SALV_TABLE)  
- File download (local PC or SAP server)  
- Adobe form integration (currently commented out)  
- Characteristic-based filtering (capacity, product group, etc.)  

---

## 📌 2. Architecture

### 2.1 Main Components
| Component              | Description                                  |
|------------------------|----------------------------------------------|
| **Selection Screen**   | Collects user inputs and filter criteria     |
| **Class LCL_ZP60_MAPEX** | Encapsulates business logic                 |
| **START-OF-SELECTION** | Entry point – connects screen and logic      |
| **ALV Grid Display**   | Displays output in SAP GUI                   |
| **File Export**        | Saves CSV data to local PC or application server |

---

## 🧾 3. Selection Screen
The selection screen is built with `SELECTION-SCREEN` blocks and allows flexible filtering.

### Key Parameters
- `P_AUFNR` – Order number  
- `P_PLNBEZ` – Material number  
- `P_WERKS` – Plant  
- `P_AUART` – Order type  
- `P_ARBPL` – Work center  
- `P_GSTRP`, `P_GLTRP` – Basic start/end dates  
- `P_DOWNLOAD` – Download mode (X = file export, else ALV display)  
- `P_PATH`, `P_PATH_SRV` – File paths  

### Characteristic Filters (via ATINN)
- **Capacity**  
- **Product group**  
- **Minimum strength**  
- **Gravity**  

---

## 🧠 4. Class: `LCL_ZP60_MAPEX`

Encapsulates logic for **selection, formatting, and output**.  

### 4.1 Method Summary
| Method         | Purpose                                       |
|----------------|-----------------------------------------------|
| `get_data`     | Retrieves data based on selection criteria    |
| `sel_header`   | Creates header-level selection conditions     |
| `sel_item`     | Creates item-level characteristic filters     |
| `display_alv`  | Displays output in ALV grid                   |
| `get_file_data`| Converts internal data → semicolon-delimited format |
| `get_file_path`| Builds timestamped file name with given path  |

### 4.2 Attributes
- `mt_alv`, `mt_file`, `mt_header`, `mt_item` – Internal tables  
- `mo_alv` – ALV grid reference  
- `mv_fname` – Generated file name  
- `mo_sel` – Selection object for filters  

---

## 💾 5. Data Flow

1. **User Input**  
   - Selection screen (order, material, plant, etc.)  
   - Characteristics & file preferences  

2. **Data Selection**  
   - Orders read from `AUFNR`, `PLNBEZ`, `WERKS`  
   - Item characteristics filtered via `sel_item`  
   - `get_data` retrieves relevant entries  

3. **Output Decision**  
   - If `P_DOWNLOAD = 'X'` → Write CSV file  
   - Else → Display ALV via `display_alv`  

4. **File Formatting**  
   - Semicolon-delimited (;)  
   - `get_file_data` handles conversion  
   - File written using `cl_gui_frontend_services` (local) or `OPEN DATASET` (server)  

---

## 🧾 6. ALV Display
- Implemented with **CL_SALV_TABLE**  
- Sorted descending by **creation date (ERDAT)**  

---

## 💾 7. File Handling
- **Local Download** → via `cl_gui_frontend_services`  
- **Application Server Write** → via `OPEN DATASET`  

---

## 🛠️ Technologies
- ABAP (Reports + OOP)  
- ALV Grid (`CL_SALV_TABLE`)  
- File Handling (`cl_gui_frontend_services`, `OPEN DATASET`)  
- SAP Classification System (Characteristics ATINN)  

---
