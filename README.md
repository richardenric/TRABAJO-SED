# TRABAJO-SED
Aquí iremos poniendo los progresos que vayamos haciendo en el trabajo, de manera que podamos ver las intervenciones, aportaciones o sugerencias de cada uno. 

MÁQUINA DE ESTADOS
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity elevator is
generic(n:positive:=2);
    Port ( boton : in std_logic_vector(n downto 0);
             clk: in STD_LOGIC;
           reset_n: in STD_LOGIC;--siempre que se active reset volvemos al piso 0
           piso : out STD_LOGIC_VECTOR(n downto 0));
end elevator;

architecture Behavioral of elevator is

type state_type is(p0,p1,p2,p3,p4);
signal actual,siguiente:state_type;--estado actual y estado siguiente
signal bot:std_logic_vector(n downto 0);--almacena el boton pulsado

begin

sync_proc:process(clk,reset_n)--bloque secuencial para calcular el estado siguiente
    begin
    if (rising_edge(clk)) then
        if(reset_n='1') then
            actual<=p0;
        else 
            actual<=siguiente;
        end if;
    end if;
end process;

output_decode:process(actual)--bloque dataflow para actualizar el estado actual
begin
case(actual)is
when p0=>piso<="000";
when p1=>piso<="001";
when p2=>piso<="010";
when p3=>piso<="011";
when p4=>piso<="100";
when others=>piso<="000";
end case;
end process;

next_state_decode:process(actual,boton)
begin
siguiente<=p0;
case(actual) is
when p0=>
if(boton="000") then
siguiente<=p0;
end if;
if(boton="001") then
siguiente<=p1;
end if;
if(boton="010") then
siguiente<=p2;
end if;
if(boton="00011") then
siguiente<=p3;
end if;
if(boton="100") then
siguiente<=p4;
end if;

when p1=>
if(boton="000") then
siguiente<=p0;
end if;
if(boton="001") then
siguiente<=p1;
end if;
if(boton="010") then
siguiente<=p2;
end if;
if(boton="011") then
siguiente<=p3;
end if;
if(boton="100") then
siguiente<=p4;
end if;

when p2=>
if(boton="000") then
siguiente<=p0;
end if;
if(boton="001") then
siguiente<=p1;
end if;
if(boton="010") then
siguiente<=p2;
end if;
if(boton="011") then
siguiente<=p3;
end if;
if(boton="100") then
siguiente<=p4;
end if;

when p3=>
if(boton="000") then
siguiente<=p0;
end if;
if(boton="001") then
siguiente<=p1;
end if;
if(boton="010") then
siguiente<=p2;
end if;
if(boton="011") then
siguiente<=p3;
end if;
if(boton="100") then
siguiente<=p4;
end if;

when p4=>
if(boton="000") then
siguiente<=p0;
end if;
if(boton="001") then
siguiente<=p1;
end if;
if(boton="010") then
siguiente<=p2;
end if;
if(boton="011") then
siguiente<=p3;
end if;
if(boton="100") then
siguiente<=p4;
end if;

when others=>siguiente<=p0;
end case;
end process;



end Behavioral;



TESTBENCH
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity elevator_tb is
--  Port ( );
end elevator_tb;

architecture Behavioral of elevator_tb is
    component elevator is
    generic(n:positive:=2);
        Port ( boton : in std_logic_vector(n downto 0);
                clk: in STD_LOGIC;
                reset_n: in STD_LOGIC;--siempre que se active reset volvemos al piso 0
                piso : out STD_LOGIC_VECTOR(n downto 0));
    end component;
    
    signal boton_int:std_logic_vector(2 downto 0):="000";
    signal clk_int:std_logic:='0';
    signal reset_int:std_logic:='0';
    signal piso_int:std_logic_vector(2 downto 0):="000";
    
    begin
    inst_elevator:elevator port map(
    boton=>boton_int,
    clk=>clk_int,
    reset_n=>reset_int,
    piso=>piso_int
);
clk_int<= NOT clk_int after  30ns;
reset_int<='0','1' after 40 ns, '0' after 60 ns,'1' after 80 ns,'0' after 100 ns;
process
begin
wait for 40 ns;
boton_int<="000";
wait for 20 ns;
boton_int<="010";
wait for 50 ns;
boton_int<="100";
wait for 30 ns;
boton_int<="011";
wait for 50 ns;
boton_int<="011";
end process;


end Behavioral;



MÓDULO ANTIRREBOTES
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity debouncer is
    Port ( clk : in STD_LOGIC;
           rst : in STD_LOGIC;
           btn_in : in STD_LOGIC;
           btn_out : out STD_LOGIC);
end debouncer;

architecture Behavioral of debouncer is
signal Q1,Q2,Q3:std_logic;
begin
process(clk)
begin
if(clk'event and clk='1') then
if(rst='0') then
Q1<='0';
Q2<='0';
Q3<='0';
else
Q1<=btn_in;
Q2<=Q1;
Q3<=Q2;
end if;
end if;
end process;
btn_out<=Q1 and Q2 and (not Q3);
end Behavioral


SINCRONIZADOR
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity sincronizador is
    Port ( sync_in : in STD_LOGIC;
           clk : in STD_LOGIC;
           sync_out : out STD_LOGIC);
end sincronizador;

architecture Behavioral of sincronizador is
constant SYNC_STAGES : integer := 3;
constant PIPELINE_STAGES : integer := 1;
constant INIT : std_logic := '0';
signal sreg : std_logic_vector(SYNC_STAGES-1 downto 0) := (others => INIT);
attribute async_reg : string;
attribute async_reg of sreg : signal is "true";
signal sreg_pipe : std_logic_vector(PIPELINE_STAGES-1 downto 0) := (others => INIT);
attribute shreg_extract : string;
attribute shreg_extract of sreg_pipe : signal is "false";

begin
process(clk)
begin
if(clk'event and clk='1')then
sreg <= sreg(SYNC_STAGES-2 downto 0) & sync_in;
end if;
end process;
no_pipeline : if PIPELINE_STAGES = 0 generate
begin
sync_out <= sreg(SYNC_STAGES-1);
end generate;
one_pipeline : if PIPELINE_STAGES = 1 generate
begin
process(clk)
begin
if(clk'event and clk='1') then
sync_out <= sreg(SYNC_STAGES-1);
end if;
end process;
end generate;
multiple_pipeline : if PIPELINE_STAGES > 1 generate
begin
process(clk)
begin
if(clk'event and clk='1') then
sreg_pipe <= sreg_pipe(PIPELINE_STAGES-2 downto 0) & sreg(SYNC_STAGES-1);
end if;
end process;
sync_out <= sreg_pipe(PIPELINE_STAGES-1);
end generate;

end Behavioral



DIVISOR DE FRECUENCIA
entity clk_divider is
generic(n:positive:=1000000);
    Port ( clk : in STD_LOGIC;
           reset : in STD_LOGIC;
           clk_out : out STD_LOGIC);
end clk_divider;

architecture Behavioral of clk_divider is
    signal temp:std_logic;--para gestionar el estado de la señal de salida
    signal counter:integer range 0 to n-1:=0;--contador que lleva la cuenta del número de cambios de estado de ña entrada de reloj
    begin
        process(clk,reset)
            begin
            if(reset='1')then
                temp<='0';
                counter<=0;
            elsif rising_edge(clk)then
                if(counter=n-1)then
                    temp<=not (temp);
                    counter<=0;
                else
                counter<=counter + 1;
                end if;
            end if;
         end process;
clk_out<=temp;

end Behavioral;
