# 📦 PLC Project – Box Wrapper

A practical **industrial automation project** simulating a **Box Wrapper Machine** – used in packaging factories and airports.  
Implemented in **CODESYS** with Ladder Logic + Structured Text.

---

## ⚙️ System Overview
- **Rotational Motor** → Spins the pallet platform  
- **Up/Down Motor** → Moves the film roll vertically  
- **Sensors** → Limit switches, rotation counter, film sensor  
- **Safety** → Emergency stop, overloads  

---

## 🖥️ PLC Logic
1. Press **Start** → pallet rotates.  
2. After **3 rotations** → motor moves **Up**.  
3. After **3 more rotations** → motor moves **Down**.  
4. Wrapping cycle ends → all motors stop.  
5. Emergency stop overrides everything.  

---

## 🔑 Inputs & Outputs

| Signal | Type | Description |
|--------|------|-------------|
| Start | BOOL | Start button |
| Stop | BOOL | Stop button |
| EMGC | BOOL | Emergency stop |
| HLSW | BOOL | High limit switch |
| LLSW | BOOL | Low limit switch |
| Rotaional_Count_PX | BOOL | Rotation counter sensor |
| Stretch_PX | BOOL | Film empty sensor |
| Rotational_M | BOOL | Rotation motor output |
| Slider_M_Up | BOOL | Up motor output |
| Slider_M_Down | BOOL | Down motor output |

---

## 📊 Project Diagrams

### 1. Machine & HMI
Screenshot 2025-09-14 165219.png

### 2. Wiring Diagram
![Wiring Diagram](https://github.com/BhaskarMandal/plc-box-wrapper/blob/diagrams/Screenshot%202025-09-14%20165837.png?raw=true)

### 3. Sensors & Connections
![Sensors & Connections](https://github.com/BhaskarMandal/plc-box-wrapper/blob/diagrams/Screenshot%202025-09-14%20165906.png?raw=true)

### 4. Control Flowchart
![Control Flowchart](https://github.com/BhaskarMandal/plc-box-wrapper/blob/diagrams/Screenshot%202025-09-14%20165950.png?raw=true)

### 5. Ladder Logic Implementation
![Ladder Logic](https://github.com/BhaskarMandal/plc-box-wrapper/blob/diagrams/Screenshot%202025-09-14%20170020.png?raw=true)


## 📜 Example Code (Structured Text)

```pascal
PROGRAM PLC_PRG
VAR
    Start, Stop, EMGC : BOOL;
    Rotational_M, Slider_M_Up, Slider_M_Down : BOOL;
    Rotaional_Count_PX, Stretch_PX, HLSW, LLSW : BOOL;
    Up_Count, Down_Count : CTU;
END_VAR

// Start cycle
IF Start AND NOT EMGC THEN
    Rotational_M := TRUE;
END_IF;

// Stop logic
IF Stop OR EMGC THEN
    Rotational_M := FALSE;
    Slider_M_Up := FALSE;
    Slider_M_Down := FALSE;
END_IF;

// Count rotations
IF Rotaional_Count_PX THEN
    Up_Count(CU := TRUE, R := FALSE, PV := 3);
    Down_Count(CU := TRUE, R := FALSE, PV := 6);
END_IF;

// Up/Down motor control
IF Up_Count.Q THEN
    Slider_M_Up := TRUE;
END_IF;

IF Down_Count.Q THEN
    Slider_M_Down := TRUE;
    Slider_M_Up := FALSE;
END_IF;
