# CP1
## Algèbre de bool
### Opération de base
And $A*B$

![image](https://user-images.githubusercontent.com/62622644/204113876-cc0c1e96-1f08-48c5-9adb-08c299f97742.png)

Ou $A+B$

![image](https://user-images.githubusercontent.com/62622644/204113974-7f3fc294-2d44-4aa0-ac94-79f83028a6f4.png)

Not $\bar A$

![image](https://user-images.githubusercontent.com/62622644/204114033-1f614ca0-4351-408e-9eb4-b9d1c6e44699.png)

### De Morgan
$\overline{A+B} = \bar A * \bar B$

$\overline{A*B} = \bar A + \bar B$

### Théorèmes
1. $x * 0 = 0$
2. $x * 1 = x$
3. $x * x = x$
4. $x * \bar x = 0$
5. $x + 0 = x$
6. $x + 1 = 1$
7. $x + x = x$
8. $x + \bar x = 1$

## VHDL (VHSIC Hardware Description Language) 
### Syntaxe
`--` commentaires

`;` fin d'instruction

`'1'` bit, `"1000000"` vecteur

`signal <nom> : <type>` définition d'un signal

`<=` assignation


### Assignations (`<=`)
#### Assignations inconditionnelles hors process
```VHDL 
A <= B AND C; A prendra toujours la valeur de B * C
``` 
```VHDL
D <= '1'; -- D prendra toujours la valeur de 1
``` 

#### Assignations conditionnelles hors process avec when else
```VHDL
E <= '1' when A='1' else B;
```
```VHDL
F <= 
  '1' when A='1' else
  '0' when B='1' else
  C;
```

#### Assignations conditionnelles hors process avec with select (utilisé pour les tables de vérités avec un bus d'entrée)
```VHDL
with AB select
E <= 
  '1' when "01",
  '1' when "10",
  '0' when others;
```

### Types
`std_logic` signal de 1 bit

`std_logic_vector(<max> downto <min>)` ou `(<min> to <max>)` signal de max à min bits (

`unsigned(<max> downto <min>)` ou `(<min> to <max>)` vecteur non signé de max à min bits

`signed(<max> downto <min>)` ou `(<min> to <max>)` vecteur signé de max à min bits

integer ??
> ❌

`boolean` valeur possible true et false

`type JOUR_SEMAINE is (lu, ma, me, je, ve, sa, di)` type personnalisé

`type T_RAM is array(... to ...) of std_logic_vector(... downto ...)` matrice

### Libraries
Dans tous les fichiers VHDL il faut inclure ces 2 lignes
```VHDL
library ieee;
use ieee.std_logic_1164.all;
```
et pour faire des calculs arithmétiques is faut inclure 
```VHDL
use ieee.numeric_std.all;
```
```VHDL
  test
```

### Entity
Une entité décrit les ports d'entrées et de sorties d'un composant (correspond à sont symbole)
```VHDL
entity multiplexeur is
  port (
    sel : in  std_logic;
    i1 : in  std_logic_vector(7 downto 0);
    i2 : in  std_logic_vector(7 downto 0);
    o : out std_logic_vector(7 downto 0)
  );
end multiplexeur;
```

### Architecture (aura besoin d'une entitiy pour décrire ses entrée est sortie)
Definit le comportement d'un composant (correspond à sont schéma électronique avec de composant élémentaire et d'autre composant)
```VHDL
architecture Behavioral of multiplexeur is
begin
  o <= i1 when sel='0' else i2;
end Behavioral;
```
> Behavioral ? ❌
```VHDL
architecture Behavioral of multiplexeur is
begin
  with sel select
    o <= 
    i1 when '0',
    i2 when others;
end Behavioral;
```

### Process (sera defini dans une architecture)
Un process regroupe des instructions qui sont évaluées les unes après les autres. 
Travail avec un flanc montant `if rising_edge(clk_i) then` ou descendant . Avec des bascule D pour chaque signal d'entrée.
Lors des assignations, la valeur des signaux est mise à jour uniquement à la fin du process.

```VHDL
process(clk_i,reset_i) -- Que les entrée qui sont évaluer à tout moment
begin 
   -- NE RIEN ECRIRE ENTRE BEGIN ET IF
   if reset_i = '1' then -- Partie asynchrone
     elsif rising_edge(clk_i) then -- Partie synchrone
   end if;
   -- NE RIEN ECRIRE ENTRE END IF ET END PROCESS
end process;
```

### Utilisation d'un componant (une entity et une architecture)
Un componant permet d'utiliser un composant déjà décrit avec une entity et une architecture 

#### La déclatration
```VHDL
component multiplexeur is
  port (
    sel : in  std_logic;
    i1 : in  std_logic_vector(7 downto 0);
    i2 : in  std_logic_vector(7 downto 0);
    o : out std_logic_vector(7 downto 0)
  );
end componant multiplexeur;
```

#### L'instanciation 
```VHDL
-- Définition de singal afin de pouvoir utiliser le composant
signal sel_internal : std_logic;
signal i1_internal : std_logic_vector(7 downto 0);
signal i2_internal : std_logic_vector(7 downto 0);
signal o_internal : std_logic_vector(7 downto 0);

-- Création de l'instance
multiplexeur_instance : multiplexeur 
  port map(
    sel => sel_internal,
    i1 => i1_internal,
    i2 => i2_internal,
    o => o_internal
  );
```
