# Process synchrone de base
```VHDL
process(clk_i,reset_i)
begin
  if reset_i = '1' then
    reg <= (others => '0'); -- pour un bit: reg <= '0';
  elsif rising_edge(clk_i) then
    if enable = '1' then
    end if;
  end if;
end process;
```

# Exemple mémoire un bit avec reset asynchrone
```VHDL
process(clk_i,reset_i)
  begin
  if reset_i = '1' then
    reg <= '0'; -- mise à 0 de la bascule D (CLR)
  elsif rising_edge(clk_i) then
    reg <= data_i; -- mémorisation de data_i (D)
  end if;
end process;

data_o <= reg; -- (sortie Q)
```

# Bascule D avec entrée enable
```VHDL
process(clk_i,reset_i)
begin
  if reset_i = '1' then
    reg <= '0';
  elsif rising_edge(clk_i) then
    if enable_i = '1' then -- reg prend la valeur de data_i
      reg <= data_i; -- si enable_i vaut '1'
    end if; -- sinon conserve sa valeur
  end if;
end process;

data_o <= reg;
```

# Bascule D avec reset synchrone
```VHDL
process(clk_i,reset_i)
begin
  if reset_i = '1' then -- reset asynchrone
    reg <= '0';
  elsif rising_edge(clk_i) then
    if reset_s_i = '1' then -- reset_s_i met reg à '0'
      reg <= '0';
    else -- sinon reg prend la valeur de data_i
      reg <= data_i;
    end if;
  end if;
end process;

data_o <= reg;
```

# Bascule T ou diviseur par 2 avec reset synchrone
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= '0'; 
  elsif rising_edge(clk_i) then
    if enable_i = '1' then
      reg <= not reg; -- idem que if reg='0' then
    end if; reg <= '1';
  end if; else 
end process;

data_o <= reg;
```

# Registre (tampon) de n bits
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0'); 
  elsif rising_edge(clk_i) then
    if enable_i = '1' then
      reg <= data_i;
    end if;
  end if;
end process;

data_o <= reg;
```
# Compteur de 8 bits avec reset synchrone
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0'); 
  elsif rising_edge(clk_i) then
    if reset_s_i='1' then -- prioritaire sur enable_i
      reg <= (others=>'0');
    elsif enable_i = '1' then
     reg <= reg + 1;
    end if;
  end if;
end process;

data_o <= std_logic_vector(reg);
```

# Compteur de 8 bits avec pré-chargement 
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0'); 
  elsif rising_edge(clk_i) then
    if load_i='1' then
      reg <= unsigned(data_i); -- <- cast
    elsif enable_i = '1' then
      reg <= reg + 1;
    end if;
  end if;
end process;

data_o <= std_logic_vector(reg); 
```

# Compteur de 0 à 9 avec retenue
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0'); 
  elsif rising_edge(clk_i) then
    if enable_i = '1' then
      if reg = 9 then -- repasse à 0
        reg <= (others => '0');
      else -- incrémentation
      reg <= reg + 1;
      end if;
    end if;
  end if;
end process;

data_o <= std_logic_vector(reg); -- attention au cast
carry_o <= '1' when reg = 9 and enable_i = '1' else '0';
```

# Diviseur par 6 (de 0 à 5)
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0');
    div_o <= '0'; 
  elsif rising_edge(clk_i) then
    div_o <= '0'; -- <- div_o toujours mis à 0 
    if enable_i = '1' then
      if reg = 5 then
        reg <= (others => '0'); -- <- reg mis à 0
        div_o <= '1'; -- <- div_o mis à 1
      else 
        reg <= reg + 1;
      end if;
    end if;
  end if;
end process;
```

# Registre à décalage à gauche 8 bits avec entrée série et sortie parallèle et série
```VHDL
signal : reg std_logic_vector(8 downto 0);
```
```VHDL
process(clk_i,reset_i)
  begin
  if reset_i='1' then
    reg <= (others => '0');
  elsif rising_edge(clk_i) then
    if enable_i = '1' then
      reg <= reg(6 downto 0) & serin_i; -- <---
    end if;
  end if;
end process;

data_o <= reg;
serout_o <= reg(7);
```

# Registre à décalage à gauche 8 bits avec entrée série et sortie parallèle et série
```VHDL
signal : reg std_logic_vector(8 downto 0);
```
```VHDL
process(clk_i,reset_i)
begin
  if reset_i='1' then
    reg <= (others => '0');
  elsif rising_edge(clk_i) then
    if load_i = '1' then
      reg <= data_i;
    elsif enable_i = '1' then
      reg <= '0' & reg(7 downto 1); -- --->
    end if;
  end if;
end process;
```
