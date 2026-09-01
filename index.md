
# Tutorial de Programación en VHDL

Bienvenido a este tutorial rápido de VHDL. Aquí encontrarás la estructura básica y un ejemplo listo para probar.

## Estructura Básica en VHDL
Todo diseño en VHDL consta de tres partes principales:
* **Libraries (Bibliotecas):** Declara las funciones estándar.
* **Entity (Entidad):** Define las entradas y salidas (los pines del chip).
* **Architecture (Arquitectura):** Define el comportamiento del circuito.

INSTALAR las herramientas necesarias

# Actualizar los repositorios del sistema
```bash
sudo apt update && sudo apt upgrade -y
```

# Instalar GHDL (el compilador de VHDL)
```bash
sudo apt install ghdl -y
```

# Instalar GTKWave (el visualizador gráfico de señales)
```bash
sudo apt install gtkwave -y
```

## INSTALAR EXTENSION EN VS CODE

Ctrl + Shift + X

instalar la extension
VHDL LS (icono verde por Henrik)

## INSTALAR DIGITAL
decargar https://github.com/hneemann/Digital
mover la carpeta a opt y luego crear el link simbolico
```bash
mv Digital/ /opt
sudo ln -s /opt/Digital/Digital.sh /usr/local/bin/digital
```

## Listo! Para empezar crear 2 archivos
```bash
mkdir compuertaOr
cd compuertaOr
touch compuerta_or.vhd
touch compuerta_or_tb.vhd
```
## Código de Ejemplo: Compuerta OR
Copia y pega este código en tu entorno de desarrollo (como Quartus o Vivado):

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY compuertaOr IS
    PORT ( a, b : IN STD_LOGIC;
            c : OUT STD_LOGIC);
END compuertaOr;

ARCHITECTURE ckt OF compuertaOr IS

BEGIN
    c <= a or b;
END ckt;
```

## Crear un archivo testbench para probar el circuito
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;


ENTITY circuitoOr_tb IS

END circuitoOr_tb;

ARCHITECTURE Smd OF circuitoOr_tb IS
    
    SIGNAL a_t : STD_LOGIC := '0';
    SIGNAL b_t : STD_LOGIC := '1';
    SIGNAL c_t : STD_LOGIC;
    
BEGIN
    UUT :ENTITY WORK.compuertaOr PORT MAP (a => a_t, b => b_t, c => c_t);
    PROCESS
    BEGIN
        a_t <= '0'; b_t <= '0'; WAIT FOR 20 ns;
        a_t <= '0'; b_t <= '1'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '0'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '1'; WAIT FOR 20 ns;
        wait;
    END 
    PROCESS;

END Smd;
```
## ejemplo de una tabla de verdad
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tabla IS
    PORT(a, b, c :IN STD_LOGIC;
        y       :OUT STD_LOGIC);
END tabla;

ARCHITECTURE smd OF tabla IS
    SIGNAL bits_ent : STD_LOGIC_VECTOR(2 DOWNTO 0);
    BEGIN
    bits_ent <= a & b & c; --concatena los bits de entrada en bit_vector
        WITH bits_ent SELECT
        y <= '0' WHEN "000",
             '0' WHEN "001",
             '0' WHEN "010",
             '1' WHEN "011",
             '0' WHEN "100",
             '1' WHEN "101",
             '1' WHEN "110",
             '1' WHEN "111",
             '0' WHEN OTHERS; --importante escribirlo, sebe considerar otras entradas posibles
END smd;
```

## creamos el testbench
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY tabla_tb IS

END tabla_tb;

ARCHITECTURE sim OF tabla_tb IS

    SIGNAL a_t : STD_LOGIC := '0';
    SIGNAL b_t : STD_LOGIC := '0';
    SIGNAL c_t : STD_LOGIC := '0';
    SIGNAL y_t : STD_LOGIC;

BEGIN
    UUT : ENTITY WORK.tabla PORT MAP(a => a_t, b => b_t, c => c_t, y => y_t);
    PROCESS
    BEGIN
        a_t <= '0'; b_t <= '0'; c_t <= '0'; WAIT FOR 20 ns;
        a_t <= '0'; b_t <= '0'; c_t <= '1'; WAIT FOR 20 ns;
        a_t <= '0'; b_t <= '1'; c_t <= '0'; WAIT FOR 20 ns;
        a_t <= '0'; b_t <= '1'; c_t <= '1'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '0'; c_t <= '0'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '0'; c_t <= '1'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '1'; c_t <= '0'; WAIT FOR 20 ns;
        a_t <= '1'; b_t <= '1'; c_t <= '1'; WAIT FOR 20 ns;
        WAIT;
    END
    PROCESS;

END sim;
```

## Ejemplo de IF-ELSE
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;

ENTITY condicional IS

PORT( valor_digital :IN STD_LOGIC_VECTOR(3 DOWNTO 0); --entrada de 4 bits
        z :OUT STD_LOGIC);

END condicional;

ARCHITECTURE decision OF condicional IS

BEGIN
    PROCESS (valor_digital)
    BEGIN
        IF(UNSIGNED(valor_digital) > 6) THEN
            z <= '1';
        ELSE
            z <= '0';
        END IF;
    END PROCESS;
END decision;
```

## Ejemplo de ELIF
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;

ENTITY condicional_elsif IS

PORT(val_dig : IN STD_LOGIC_VECTOR(3 downto 0); --declara la entrada de 4 bits
    frio, temp, calor : OUT STD_LOGIC);
END condicional_elsif;

ARCHITECTURE quetancaliente OF condicional_elsif IS
SIGNAL estado :STD_LOGIC_VECTOR (2 downto 0);
BEGIN
    PROCESS (val_dig)
        BEGIN
            IF(unsigned(val_dig) <= 8) THEN estado <= "100";
            ELSIF (unsigned(val_dig) > 8 AND unsigned(val_dig) < 11) THEN
                estado <= "010";
            ELSE estado <= "001";
            END IF;
        END PROCESS;
    frio <= estado(2); --asigna los bits de estado a la salida
    temp <= estado(1);
    calor <= estado(0);
END quetancaliente;
```

## ejemplo de case
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

ENTITY condicion_case IS
PORT(p, q, r :IN STD_LOGIC;
    s :OUT STD_LOGIC);
END condicion_case;

ARCHITECTURE copia OF condicion_case IS
SIGNAL estado :STD_LOGIC_VECTOR (2 downto 0);
BEGIN
    estado <= p & q & r;    --concatena los bits en ese orden pqr
    PROCESS (estado)
    BEGIN
        CASE estado IS
            WHEN "100" => s <= '0';
            WHEN "101" => s <= '0';
            WHEN "110" => s <= '0';
            WHEN OTHERS => s <= '1';
        END CASE;
    END PROCESS;
END copia;
```

## ejemplo de una maquina traga monedas
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;

ENTITY maquina_traga_modendas IS

PORT( q, d, n: IN STD_LOGIC;
    centavos :OUT STD_LOGIC_VECTOR(3 downto 0));
END maquina_traga_modendas;

ARCHITECTURE detector OF maquina_traga_modendas IS
    SIGNAL monedas :STD_LOGIC_VECTOR(2 DOWNTO 0); --agrupa los sensores de monedas
    BEGIN
        monedas <= (q & d & n);
        PROCESS (monedas)
            BEGIN
                CASE (monedas) IS
                    WHEN "001" => centavos <= "0101";
                    WHEN "010" => centavos <= "1010";
                    WHEN "100" => centavos <= "1111";
                    WHEN OTHERS => centavos <= "0000";
                END CASE;
            END PROCESS;
        END detector;
```
