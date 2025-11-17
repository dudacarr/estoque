# Banco de dados
import pandas as pd
import matplotlib.pyplot as plt

def obter_dados_item():
  # Obtem dados do produto
  codigo = input("\nCódigo do item: ").strip()
  descricao = input("Descrição do Item: ").strip()
  while True:
    escolha = input("\nCategoria: Digite 1 para Matéria-Prima ou 2 para Produto acabado:")
    if escolha == "1":
      categoria = "Matéria-Prima"
      break
    elif escolha == "2":
      categoria = "Produto Acabado"
      break
    else:
      print("\nEntrada inválida. Digite 1 ou 2.")
  unidade = input("\nUnidade (ex: kg, L, un): ").strip()
  quantidade = int(input("\nQuantidade inicial do produto: "))
  preco = input("\nPreço unitário do produto: ")
  return {"Código": codigo,  "Item": descricao, "Categoria": categoria, "Unidade": unidade, "Quantidade": quantidade, "Preço": preco}

def excluir_produto(estoque_lista):
  # Exclui um produto
  id_excluir = input("\nDigite o Código do produto que você deseja excluir: ").strip()
  for produto in estoque_lista:
    if produto['Código'] == id_excluir:
      estoque_lista.remove(produto)
      print(f"'{produto['Item']}' excluído!")
      return
  else:
    print("\nProduto não encontrado.")

def adicionar_ao_estoque(estoque_lista, produto):
# Adiciona um produto
  estoque_lista.append(produto)
  print(f"'{produto['Item']}' cadastrado!")

def mostrar_pilha(estoque_lista):
  # Mostra a pilha de itens do último para o primeiro
  if not estoque_lista:
    print("\n--- Pilha vazia ---")
    return
  print("\n--- Pilha ---")
  print(f"{'Código':10} {'Item':20} {'Categoria':20} {'Unidade':10} {'Quantidade':6} {'Preço':10}")
  print("-" * 82)
  for produto in reversed(estoque_lista):    # Para imprimir ao contrário
    alerta = "ATENÇÃO! ESTOQUE BAIXO!" if produto['Quantidade'] < 5 else "" #Alerta para menor que 5
    print(f"{produto['Código']:10} {produto['Item']:20} {produto['Categoria']:20} {produto['Unidade']:10} {produto['Quantidade']:6} {produto['Preço']:10} {alerta}")

def movimentar_produto(estoque_lista):
  # Lança/Baixa de produtos
    id_mov = input("\nDigite o Código do produto que você deseja movimentar: ").strip()
    encontrado = False
    for produto in estoque_lista:
      if produto['Código'] == id_mov:
        encontrado = True
        print(f"\nProduto encontrado: {produto['Item']} (Estoque atual: {produto['Quantidade']})")
        tipo = input("\nDigite '1' para entrada ou '2' para saída: ")
        if tipo == "1":
          quantidade_mov = int(input("Digite a quantidade a ser adicionada: "))
          produto['Quantidade'] += quantidade_mov
          print(f"\nUnidades adicionadas ao estoque. Novo estoque: {produto['Quantidade']}")
        elif tipo == "2":
          quantidade_mov = int(input("\nDigite a quantidade a ser retirada: "))
          if produto['Quantidade'] >= quantidade_mov:
            produto['Quantidade'] -= quantidade_mov
            print(f"Unidades retiradas do estoque. Novo estoque: {produto['Quantidade']}")
          else:
            print("\nQuantidade insuficiente em estoque.")
        else:
          print("\nOpção inválida.")
        break
    if not encontrado:
      print("\nProduto não encontrado.")

def exibir_tabela_estoque(estoque_lista):
  # Para exibir a tabela (normal)
    print("\n--- Lista completa de itens (ordem de cadastro) ---")
    if not estoque_lista:
        print("Nenhum item cadastrado.")
        return
    print(f"{'Código':10} {'Item':20} {'Categoria':20} {'Unidade':10} {'Quantidade':6} {'Preço':10}")
    print("-" * 82) # Ajustado o tamanho da linha para incluir o Alerta
    for p in estoque_lista:
      alerta = "ATENÇÃO! ESTOQUE BAIXO!" if p['Quantidade'] < 5 else ""
      print(f"{p['Código']:10} {p['Item']:20} {p['Categoria']:20} {p['Unidade']:10} {p['Quantidade']:6} {p['Preço']:10} {alerta}")

def exportar_banco(estoque_lista):
  #Para exportar o banco de dados
  if not estoque_lista:
    print("\nNenhum item cadastrado. Nada a exportar")
    return

  # Minimenu para exportação
  print("\n --- Opções de exportação ---")
  print("1. Exportar para Excel")
  print("2. Exportar para CSV")
  print("3. Voltar ao menu principal")

  escolha = input("\nDigite o número da opção desejada: ").strip()

  df = pd.DataFrame(estoque_lista)
  if escolha == "1":
    df.to_excel("estoque.xlsx", index=False)
    print("Banco de dados exportado com sucesso!")
  elif escolha == "2":
    df.to_csv("estoque.csv", index=False)
    print("Banco de dados exportado com sucesso!")
  elif escolha == "3":
    print("Voltando ao menu principal.")
  else:
    print("Opção inválida.")

def relatorios_gerenciais(estoque_lista):
    if not estoque_lista:
        print("\nNenhum item cadastrado. Não há dados para gerar relatórios.")
        return

    print("\n--- RELATÓRIOS GERENCIAIS ---")
    print("1. Giro de Estoque")
    print("2. Custo de Manutenção do Estoque")
    print("3. Tempo Médio de Reposição (simples)")
    print("4. Estoque de Segurança (simples)")
    print("5. Voltar ao menu principal")

    op = input("\nEscolha uma opção: ")


    if op == "1":
        print("\n--- Giro de Estoque ---")
        print("Cálculo simples: Giro = Vendas / Estoque Médio")
        vendas = float(input("Digite o total vendido no período (em unidades): "))
        estoque_medio = float(input("Digite o estoque médio no período: "))

        if estoque_medio == 0:
            print("Não é possível calcular, estoque médio = 0.")
        else:
            giro = vendas / estoque_medio
            print(f"Giro de estoque aproximado: {giro:.2f}")

    elif op == "2":
        print("\n--- Custo de Manutenção do Estoque ---")
        print("Cálculo simples: Custo = Estoque Total × Taxa Anual (%)")

        taxa = float(input("Digite a taxa anual de manutenção (%): "))

        custo_total_estoque = 0
        for p in estoque_lista:
            try:
                preco = float(p["Preço"])
                custo_total_estoque += preco * p["Quantidade"]
            except:
                pass  # ignora itens com preços inválidos


        custo = custo_total_estoque * (taxa / 100)
        print(f"Valor total do estoque: R$ {custo_total_estoque:.2f}")
        print(f"Custo anual de manutenção: R$ {custo:.2f}")

    elif op == "3":
        print("\n--- Tempo Médio de Reposição ---")
        print("Cálculo simples: (Soma dos tempos) / quantidade de reposições")

        qtd = int(input("Quantas reposições ocorreram no mês? "))
        if qtd == 0:
            print("Não é possível calcular, pois não houve reposições.")
            return

        soma = 0
        for i in range(qtd):
            dias = int(input(f"Digite os dias da reposição {i+1}: "))
            soma += dias

        media = soma / qtd
        print(f"Tempo médio de reposição: {media:.1f} dias")


    elif op == "4":
        print("\n--- Estoque de Segurança ---")
        print("Cálculo básico: ES = (Consumo médio × Tempo de reposição)")

        consumo = float(input("Consumo médio diário (unidades/dia): "))
        tempo = float(input("Tempo de reposição em dias: "))

        es = consumo * tempo

        print(f"Estoque de segurança sugerido: {es:.1f} unidades")

    elif op == "5":
        print("Voltando ao menu principal...")
        return

    else:
        print("Opção inválida.")

    input("\nPressione Enter para continuar...")


def graficos_estoque(estoque_lista):
  # Função para gráficos
  if not estoque_lista:
    print("Nenhum item cadastrado. Não há dados para gerar gráficos.")
    return

  # Minimenu para gráficos
  while True:
    print("\n--- Dashboard de Gráficos ---")
    print("1. Evolução do estoque no tempo (linhas)")
    print("2. Comparação de categorias de produtos (barras)")
    print("3. Custo de estoque (Curva ABC)")
    print("4. Voltar ao menu principal")

    op = input("\nEscolha uma opção: ").strip()

    df = pd.DataFrame(estoque_lista)
    df["Preço"] =pd.to_numeric(df["Preço"], errors="coerce")
    df["Custo Total"] = df["Preço"] * df["Quantidade"]

    # Gráfico de Linhas
    if op == "1":
      plt.figure()
      plt.plot(df["Item"], df["Quantidade"], marker="o")
      plt.title("Evolução do Estoque por Produto")
      plt.xlabel("Produto")
      plt.ylabel("Quantidade em Estoque")
      plt.xticks(rotation=45)
      plt.tight_layout()
      plt.show()

    # Gráfico de Barras
    elif op == "2":
      categorias = df.groupby("Categoria")["Quantidade"].sum()
      plt.figure()
      categorias.plot(kind="bar")
      plt.title("Total de Estoque por Categoria")
      plt.xlabel("Categoria")
      plt.ylabel("Quantidade Total")
      plt.show()

    # Curva ABC
    elif op == "3":
      df["Preço"] = df["Preço"].astype(str).str.replace(",", ".", regex=False)
      df["Preço"] = pd.to_numeric(df["Preço"], errors="coerce")

      df["Custo Total"] = df["Preço"] * df["Quantidade"] #para calcular

      df_abc = df.sort_values(by="Custo Total", ascending=False).reset_index(drop=True)  #decrescente

      # Criar percentuais acumulados
      df_abc["% Valor Acumulado"] = (df_abc["Custo Total"].cumsum() / df_abc["Custo Total"].sum()) * 100
      df_abc["% Itens Acumulado"] = (df_abc.index + 1) / len(df_abc) * 100

      # Limites para as classes - ABC
      limite_A = 80
      limite_B = 95  # 80% + 15%

      # Início
      plt.figure(figsize=(10, 6))

      plt.plot(
          df_abc["% Itens Acumulado"],
          df_abc["% Valor Acumulado"],
          marker="o",
          linestyle="-",
      )

      # Linha A
      plt.axhline(y=limite_A, linestyle="--", color="red")
      # Linha B
      plt.axhline(y=limite_B, linestyle="--", color="green")

      # 80% acumulado
      plt.axvline(
          x=df_abc["% Itens Acumulado"][df_abc["% Valor Acumulado"] <= limite_A].max(),
          linestyle="--",
          color="red",
      )

      # 95% acumulado
      plt.axvline(
          x=df_abc["% Itens Acumulado"][df_abc["% Valor Acumulado"] <= limite_B].max(),
          linestyle="--",
          color="green",
      )

      plt.title("Curva ABC - Classificação de Estoque")
      plt.xlabel("Percentual Acumulado de Itens (%)")
      plt.ylabel("Percentual Acumulado de Valor (%)")
      plt.grid(True, linestyle=":", alpha=0.6)
      plt.tight_layout()
      plt.show()

    elif op == "4":
      print("\nVoltando ao menu principal...")
      break
    else:
      print("Opção inválida.")

    input("\nPressione Enter para continuar...")


estoque = []

while True:
    print("\n---Menu Principal---")
    print("1. Cadastrar novo produto")
    print("2. Excluir produto cadastrado")
    print("3. Lançar/Baixar produto")
    print("4. Visualizar todos os produtos cadastrados")
    print("5. Exportar banco de dados")
    print("6. Relatórios gerenciais.")
    print("7. Gerar gráficos")
    print("8. Sair do sistema.")

    opcao = input("\nDigite o número da opção desejada: ")

    if opcao == "1":
      while True:
        novo_item = obter_dados_item()
        adicionar_ao_estoque(estoque, novo_item)
        continuar = input("Deseja cadastrar outro item? (s/n):").lower().strip()
        if continuar != "s":
          break
      print("\n--- Lista completa em forma de Pilha ---")
      mostrar_pilha(estoque)

    elif opcao == "2":
      excluir_produto(estoque)

    elif opcao == "3":
      movimentar_produto(estoque)

    elif opcao == "4":
      exibir_tabela_estoque(estoque)

    elif opcao == "5":
      exportar_banco(estoque)

    elif opcao == "6":
      relatorios_gerenciais(estoque)

    elif opcao == "7":
      graficos_estoque(estoque)



    elif opcao == "8":
      print("Saindo do sistema.")
      break

    elif opcao not in ["1", "2", "3", "4", "5", "6", "7", "8"]:
      print("Opção inválida. Por favor, escolha um número entre 1 e 8.")

    else:
      print("Opção inválida.")
