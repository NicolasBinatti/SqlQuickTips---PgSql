# SqlQuickTips
<h4>Algumas Dicas/Utilidades para PgSQL</h4><br><br>


- Retorna o maior valor entre eles
```
  select greatest(1,3,5) 
```
<br>

- Retorna o menorvalor entre eles
```
  select least(1,3,5) 
```
<br>

- Quebra de Linha
```
  select regexp_split_to_table('hello world', ' ')
```
<br>

- Pega a prosição do texto quebrando pelo segundo parametro da função
```
  select split_part('hello world', ' ', 2)
```
<br>

- Concatena o texto, elimina repetidos e ordena
```
  select string_agg(distinct 'teste', ',' order by nome as)
```
<br>

- Remove Todos acentos
```
  select to_ascii('áããÓÒÂaçÇ')
```
<br>

- Valida se a string é somente numeros
```
  select 'pesquisa' ~ '^[-0-9]+$'
```
<br>

- Converte para positivo
```
  select abs(-200) 
```

- Arredonda para o próximo inteiro superior
```
select CEIL(numeric) 
```
<br>

- Valor aleatório (Interessante usar na ordenação ```order by RANDOM()```)
```
Select RANDOM()
```
<br>

- Arredonda para o inteiro mais próximo, no segundo parametro, você pode configurar a dimenssão das casas decimais

```
ROUND(110.0813, 2) -- 110.08
```
<br>

 - Agrupar os dados em um array. <br>
 *Obs: o ```distinct``` irá remover registros duplicados no array, o ```FILTER (where nome is not null)``` filtrará para não vir dados nulos, funciona como um ```where``` normal da query.<br>
 Voce tambem poder usar o ```order by``` dentro do ```array_agg```, porem não funciona se já estiver usando ```distinct```
```
select  array_agg(distinct nome) FILTER (where nome is not null) as agg
```
<br>

- Varios Updates, com uma Query ou de outra tabela.
```
UPDATE tabela_a a SET
	descricao = b.descricao
FROM
  (select id, descricao from tabela_b)b
WHERE
a.idperfil = b.idperfil
```
<br>
<br>

<h4> Agora, um pouco mais avançado ! </h4> <br>

- Extrair o valor de uma chave do json
```
  select json_extract_path('{"f5":{"b" : "ovo"},"f6":"foo"}','f5') 
```
<br>

- Cria uma "tabela" a partir de uma string
```
  select
    cast(g.grade[1] as VARCHAR)  as id,
    cast(g.grade[2] as VARCHAR)  as descricao
  from 
    unnest(string_to_array('387,Teste 01;388,Teste 02;393,Teste 033',';')) v(grade)  
    left join lateral string_to_array(v.grade,',') g(grade) on true
```
<br>

- Insere, caso tenha confilto de chave, somente ignora a inserção.
```
  INSERT INTO tabela (nome, pagador, localidade) VALUES 
    ('The Best of the World', true, 'CA') 
  ON CONFLICT (id, nome) DO NOTHING;
```
<br>

- Insere, caso tenha confilto de chave, faz update dos dados.
```
INSERT INTO table (nome, pagador, localidade)
  ('Irineu', false, 'BR')
ON CONFLICT (id) DO UPDATE SET 
  nome = EXCLUDED.nome
  pagador = EXCLUDED.pagador
  localidade = EXCLUDED.localidade
RETURNING *;
```

- Concta com outro banco de dados
 ```
select * from dblink('hostaddr=***.***.***.*** port=**** dbname=dbname user=usr password=pass','
    select
       u.id, 
       u.nome
    from usuario u
    order by 1
 ')tb2 
 (
    id integer,
    nome varchar        
 )
```
<br>

- Converte uma consulta em um Json
```
   select 
    d.idequipe, 
    array_to_json(array_agg(row_to_json( d ))) as descricao
   from tabela d
   group by 1
```
<br>

- Pega valor anterior da linha de cima
Para mais explicação: [Amazon](https://docs.aws.amazon.com/pt_br/redshift/latest/dg/r_WF_LEAD.html)
```
  select lead()
```
<br>
