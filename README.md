import tkinter as tk
from tkinter import messagebox

class Festa:
    def __init__(self, nome, tabela_precos):
        self.nome = nome
        self.tabela_precos = tabela_precos
        self.vendidos = {categoria: 0 for categoria in tabela_precos}

    def ingressos_disponiveis(self, categoria):
        capacidade = self.tabela_precos[categoria]['capacidade']
        vendidos = self.vendidos.get(categoria, 0)
        return capacidade - vendidos

    def vender_ingresso(self, categoria, quantidade):
        if quantidade <= self.ingressos_disponiveis(categoria):
            self.vendidos[categoria] += quantidade
            return True
        return False

    def preco_ingresso(self, categoria):
        return self.tabela_precos[categoria]['preco']

class App:
    def __init__(self, root):
        self.tabela_precos = {
            "Ingresso Comum": {"preco": 100.00, "capacidade": 50},
            "Ingresso VIP": {"preco": 250.00, "capacidade": 30},
            "Ingresso Camarote": {"preco": 500.00, "capacidade": 20},
        }
        self.festa = Festa("Festival Exclusivo", self.tabela_precos)

        self.root = root
        self.root.title("Venda de Ingressos - Festival Exclusivo")
        self.root.geometry("600x600")
        self.root.configure(bg="#1B1B2F")

        self.font_title = ("Segoe UI", 24, "bold")
        self.font_label = ("Segoe UI", 14)
        self.font_button = ("Segoe UI", 14, "bold")

        self.header_frame = tk.Frame(root, bg="#16213E")
        self.header_frame.pack(fill="x")

        self.label = tk.Label(self.header_frame, text="🎵 Festival Exclusivo 2024 🎉", fg="#F9F871", bg="#16213E", font=self.font_title)
        self.label.pack(pady=15)

        # Tabela de preços
        self.tabela_frame = tk.Frame(root, bg="#1B1B2F")
        self.tabela_frame.pack(pady=10, fill="x", padx=20)

        tk.Label(self.tabela_frame, text="Categoria", fg="#E94560", bg="#1B1B2F", font=self.font_label, width=20, anchor="w").grid(row=0, column=0)
        tk.Label(self.tabela_frame, text="Preço (R$)", fg="#E94560", bg="#1B1B2F", font=self.font_label, width=15).grid(row=0,column=1)
        tk.Label(self.tabela_frame, text="Capacidade", fg="#E94560", bg="#1B1B2F", font=self.font_label, width=15).grid(row=0,column=2)
        tk.Label(self.tabela_frame, text="Disponível", fg="#E94560", bg="#1B1B2F", font=self.font_label, width=15).grid(row=0,column=3)

        for i, (categoria, info) in enumerate(self.tabela_precos.items(), start=1):
            tk.Label(self.tabela_frame, text=categoria, fg="white", bg="#1B1B2F", font=self.font_label, anchor="w", width=20).grid(row=i, column=0)
            tk.Label(self.tabela_frame, text=f"R$ {info['preco']:.2f}", fg="white", bg="#1B1B2F", font=self.font_label, width=15).grid(row=i,column=1)
            tk.Label(self.tabela_frame, text=f"{info['capacidade']}", fg="white", bg="#1B1B2F", font=self.font_label, width=15).grid(row=i,column=2)
            disponivel = self.festa.ingressos_disponiveis(categoria)
            self.labels_disponiveis = {}
            lbl = tk.Label(self.tabela_frame, text=f"{disponivel}", fg="#00FF00" if disponivel > 0 else "#FF0000", bg="#1B1B2F", font=self.font_label, width=15)
            lbl.grid(row=i,column=3)
            self.labels_disponiveis[categoria] = lbl

        # Seletor categoria
        self.frame = tk.Frame(root, bg="#1B1B2F")
        self.frame.pack(pady=20)

        self.quantidade_label = tk.Label(self.frame, text="Quantidade de Ingressos:", fg="#E94560", bg="#1B1B2F", font=self.font_label)
        self.quantidade_label.grid(row=1, column=0, sticky="w", pady=(0,10))

        self.quantidade_entry = tk.Entry(self.frame, font=self.font_label, width=10, bg="#0F3460", fg="white", insertbackground="white")
        self.quantidade_entry.grid(row=1, column=1, pady=(0,10), padx=10)

        self.categoria_label = tk.Label(self.frame, text="Categoria:", fg="#E94560", bg="#1B1B2F", font=self.font_label)
        self.categoria_label.grid(row=0, column=0, sticky="w", pady=(0,10))

        self.categoria_var = tk.StringVar()
        self.categoria_var.set(list(self.tabela_precos.keys())[0])  # default category

        self.categoria_menu = tk.OptionMenu(self.frame, self.categoria_var, *self.tabela_precos.keys())
        self.categoria_menu.config(font=self.font_label, bg="#0F3460", fg="white", width=15)
        self.categoria_menu["menu"].config(bg="#1B1B2F", fg="white")
        self.categoria_menu.grid(row=0, column=1, pady=(0,10), padx=10)

        self.comprar_button = tk.Button(self.frame, text="Comprar Ingressos", command=self.comprar_ingressos, bg="#E94560", fg="white", font=self.font_button, activebackground="#D72631", activeforeground="white", bd=0, padx=10, pady=8)
        self.comprar_button.grid(row=2, column=0, columnspan=2, sticky="ew", pady=10)

        self.verificar_button = tk.Button(self.frame, text="Verificar Ingressos Disponíveis", command=self.verificar_ingressos, bg="#0091D5", fg="white", font=self.font_button, activebackground="#0077B6", activeforeground="white", bd=0, padx=10, pady=8)
        self.verificar_button.grid(row=3, column=0, columnspan=2, sticky="ew", pady=10)

        self.sair_button = tk.Button(root, text="Sair", command=root.quit, bg="#E94560", fg="white", font=self.font_button, activebackground="#D72631", activeforeground="white", bd=0, padx=10, pady=8)
        self.sair_button.pack(pady=20, ipadx=10, ipady=5)

        self.footer = tk.Label(root, text="© 2024 Festival Exclusivo. Todos os direitos reservados.", fg="#7B7F9E", bg="#1B1B2F", font=("Segoe UI", 10))
        self.footer.pack(side="bottom", pady=10)

        self.frame.columnconfigure(0, weight=1)
        self.frame.columnconfigure(1, weight=0)

    def atualizar_tabela(self):
        for categoria, lbl in self.labels_disponiveis.items():
            disponivel = self.festa.ingressos_disponiveis(categoria)
            lbl.config(text=f"{disponivel}", fg="#00FF00" if disponivel > 0 else "#FF0000")

    def comprar_ingressos(self):
        categoria = self.categoria_var.get()
        try:
            quantidade = int(self.quantidade_entry.get())
            if quantidade <= 0:
                raise ValueError("A quantidade deve ser maior que zero.")
            if self.festa.vender_ingresso(categoria, quantidade):
                preco_unit = self.festa.preco_ingresso(categoria)
                total = preco_unit * quantidade
                messagebox.showinfo("Sucesso", f"{quantidade} ingresso(s) de '{categoria}' vendido(s) com sucesso!\nTotal: R$ {total:.2f} 🎉")
                self.quantidade_entry.delete(0, tk.END)
                self.atualizar_tabela()
            else:
                messagebox.showwarning("Erro", "Não há ingressos suficientes disponíveis. 😞")
        except ValueError as e:
            messagebox.showerror("Erro", str(e))

    def verificar_ingressos(self):
        categoria = self.categoria_var.get()
        disponiveis = self.festa.ingressos_disponiveis(categoria)
        messagebox.showinfo("Ingressos Disponíveis", f"Ingressos disponíveis para '{categoria}': {disponiveis} 🎫")

if __name__ == "__main__":
    root = tk.Tk()
    app = App(root)
    root.mainloop()

