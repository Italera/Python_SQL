# Python_SQL
 
# Sistema de Controle de Estoque com SQL e Python
### Este README fornece um guia passo a passo para configurar e executar um sistema de controle de estoque usando Python com uma interface gráfica criada com Tkinter e um banco de dados SQLite.

## Pré-requisitos
Antes de começar, certifique-se de ter os seguintes componentes instalados em seu sistema:

- Python 3.x
- Bibliotecas Python necessárias: tkinter, pyodbc
Você pode instalar o pyodbc usando o comando: pip install pyodbc

## Estrutura do Projeto
- main.py: Contém o código principal para o sistema de controle de estoque.
- janela/: Pasta que contém os arquivos de imagem necessários para a interface gráfica.

## Configuração do Banco de Dados
1 - Criar o Banco de Dados SQLite:

Se você ainda não possui um banco de dados SQLite configurado, crie um usando o seguinte comando no terminal: 
sqlite3 Estoque.db

2 - Criar a Tabela de Estoque:

Dentro do console SQLite, execute o seguinte comando SQL para criar a tabela Estoque:

CREATE TABLE Estoque (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    Produto TEXT NOT NULL,
    Quantidade INTEGER NOT NULL,
    DataValidade TEXT NOT NULL,
    Lote INTEGER NOT NULL
);

# Código Principal
Salve o seguinte código Python em um arquivo chamado main.py:

from tkinter import *
import pyodbc

# Configuração da conexão com o banco de dados
dados_conexao = ("Driver={SQLite3 ODBC Driver};Server=localhost;Database=Estoque.db")
conexao = pyodbc.connect(dados_conexao)
cursor = conexao.cursor()

# Função para adicionar insumo
def adicionar_insumo():
    cursor.execute(f'''
    INSERT INTO Estoque (Produto, Quantidade, DataValidade, Lote)
    VALUES
    ("{nome_insumo.get()}", {qtde_insumo.get()}, "{data_insumo.get()}", {lote_insumo.get()})
    ''')
    cursor.commit()
    caixa_texto.delete("1.0", END)
    caixa_texto.insert("1.0", f"{nome_insumo.get()} adicionado com sucesso!")

# Função para deletar insumo
def deletar_insumo():
    if len(nome_insumo.get()) < 2:
        caixa_texto.delete("1.0", END)
        caixa_texto.insert("1.0", "Nome do Insumo inválido!")
        return
    cursor.execute(f'''
    DELETE FROM Estoque 
    WHERE Produto="{nome_insumo.get()}"
    ''')
    cursor.commit()
    caixa_texto.delete("1.0", END)
    caixa_texto.insert("1.0", f"{nome_insumo.get()} deletado com sucesso!")    

# Função para consumir insumo
def consumir_insumo():
    if len(nome_insumo.get()) < 2 or len(lote_insumo.get()) < 1 or len(qtde_insumo.get()) < 1:
        caixa_texto.delete("1.0", END)
        caixa_texto.insert("1.0", "Nome, Lote e Quantidade do Insumo inválidos!")
        return
    cursor.execute(f'''
    UPDATE Estoque
    SET Quantidade=Quantidade-{qtde_insumo.get()}
    WHERE Produto="{nome_insumo.get()}" AND Lote={lote_insumo.get()}
    ''')
    cursor.commit()
    caixa_texto.delete("1.0", END)
    caixa_texto.insert("1.0", f"{nome_insumo.get()} foi consumido em {qtde_insumo.get()} unidades!") 

# Função para visualizar insumo
def visualizar_insumo():
    if len(nome_insumo.get()) < 2:
        caixa_texto.delete("1.0", END)
        caixa_texto.insert("1.0", "Nome do Insumo inválido!")
        return
    cursor.execute(f'SELECT * FROM Estoque WHERE Produto="{nome_insumo.get()}"')
    valores = cursor.fetchall()
    texto = ""
    for id_produto, nome, quantidade, validade, lote in valores:
        texto += f'''
        -----
        Produto: {nome}
        Quantidade: {quantidade}
        Validade: {validade}
        Lote: {lote}
        '''
    caixa_texto.delete("1.0", END)
    caixa_texto.insert("1.0", texto) 

# Criação da Janela
window = Tk()
window.geometry("711x646")
window.configure(bg = "#ffffff")
canvas = Canvas(window, bg = "#ffffff", height = 646, width = 711, bd = 0, highlightthickness = 0, relief = "ridge")
canvas.place(x = 0, y = 0)

background_img = PhotoImage(file = "janela/background.png")
background = canvas.create_image(355.5, 323.0, image=background_img)

img0 = PhotoImage(file = "janela/img0.png")
b0 = Button(image = img0, borderwidth = 0, highlightthickness = 0, command = visualizar_insumo, relief = "flat")
b0.place(x = 479, y = 195, width = 178, height = 38)

img1 = PhotoImage(file = "janela/img1.png")
b1 = Button(image = img1, borderwidth = 0, highlightthickness = 0, command = deletar_insumo, relief = "flat")
b1.place(x = 247, y = 197, width = 178, height = 36)

img2 = PhotoImage(file = "janela/img2.png")
b2 = Button(image = img2, borderwidth = 0, highlightthickness = 0, command = consumir_insumo, relief = "flat")
b2.place(x = 479, y = 123, width = 178, height = 35)

img3 = PhotoImage(file = "janela/img3.png")
b3 = Button(image = img3, borderwidth = 0, highlightthickness = 0, command = adicionar_insumo, relief = "flat")
b3.place(x = 247, y = 125, width = 178, height = 34)

entry0_img = PhotoImage(file = "janela/img_textBox0.png")
entry0_bg = canvas.create_image(455.0, 560.0, image = entry0_img)

caixa_texto = Text(bd = 0, bg = "#ffffff", highlightthickness = 0)
caixa_texto.place(x = 250, y = 502, width = 410, height = 114)

entry1_img = PhotoImage(file = "janela/img_textBox1.png")
entry1_bg = canvas.create_image(517.0, 294.5, image = entry1_img)

nome_insumo = Entry(bd = 0, bg = "#ffffff", highlightthickness = 0)
nome_insumo.place(x = 377, y = 278, width = 280, height = 31)

entry2_img = PhotoImage(file = "janela/img_textBox2.png")
entry2_bg = canvas.create_image(517.0, 340.5, image = entry2_img)

data_insumo = Entry(bd = 0, bg = "#ffffff", highlightthickness = 0)
data_insumo.place(x = 377, y = 324, width = 280, height = 31)

entry3_img = PhotoImage(file = "janela/img_textBox3.png")
entry3_bg = canvas.create_image(517.0, 388.5, image = entry3_img)

lote_insumo = Entry(bd = 0, bg = "#ffffff", highlightthickness = 0)
lote_insumo.place(x = 377, y = 372, width = 280, height = 31)

entry4_img = PhotoImage(file = "janela/img_textBox4.png")
entry4_bg = canvas.create_image(517.0, 436.5, image = entry4_img)

qtde_insumo = Entry(bd = 0, bg = "#ffffff", highlightthickness = 0)
qtde_insumo.place(x = 377, y = 420, width = 280, height = 31)

window.resizable(False, False)
window.mainloop()

cursor.close()
conexao.close()

#  Executando o Sistema
1 - Coloque todas as imagens necessárias (background.png, img0.png, img1.png, img2.png, img3.png, img_textBox0.png, img_textBox1.png, img_textBox2.png, img_textBox3.png, img_textBox4.png) na pasta janela.

2 - Execute o arquivo main.py:
python main.py

A interface gráfica aparecerá, permitindo que você adicione, delete, consuma e visualize insumos no estoque.

# Funcionalidades

- Adicionar Insumo: Adiciona um novo insumo ao banco de dados com nome, quantidade, data de validade e lote.
- Deletar Insumo: Remove um insumo do banco de dados com base no nome.
- Consumir Insumo: Reduz a quantidade de um insumo específico com base no nome e lote.
- Visualizar Insumo: Exibe todas as informações de um insumo específico com base no nome.

# Conclusão

Este sistema de controle de estoque é uma aplicação simples que utiliza Python e SQLite para gerenciar e manter o controle de produtos. Sinta-se à vontade para expandir e melhorar o sistema conforme suas necessidades.
