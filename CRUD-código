#app.py
# -*- coding: utf-8 -*-

import os
import tkinter as tk
from tkinter import ttk, messagebox
from decimal import Decimal, InvalidOperation
from pathlib import Path
from dotenv import load_dotenv
from supabase import create_client


# ============================================================
# CONFIGURAÇÃO DO SUPABASE
# ============================================================

load_dotenv()

url = os.getenv("SUPABASE_URL")
key = os.getenv("SUPABASE_KEY")

supabase = create_client(url, key)

print("Conexão configurada!")


# ============================================================
# CORES
# ============================================================


COR_ROXO = "#4A0080"
COR_ROXO_CLARO = "#7B2FBE"
COR_FUNDO = "#F8F0FC"
COR_BRANCO = "#FFFFFF"


# ============================================================
# APLICAÇÃO
# ============================================================

class ClinicaApp:

    def __init__(self, root):

        self.root = root

        self.root.title("Clínica Veterinária - Gerenciamento")
        self.root.geometry("1050x690")
        self.root.minsize(820, 560)
        self.root.configure(bg=COR_FUNDO)

        # ----------------------------------------------------
        # ESTILO
        # ----------------------------------------------------

        estilo = ttk.Style()
        estilo.theme_use("clam")

        estilo.configure(
            "TNotebook",
            background=COR_FUNDO
        )

        estilo.configure(
            "TNotebook.Tab",
            padding=(12, 7)
        )

        estilo.configure(
            "Treeview",
            rowheight=25
        )

        estilo.configure(
            "Treeview.Heading",
            background=COR_ROXO,
            foreground=COR_BRANCO,
            font=("Arial", 9, "bold")
        )

        estilo.map(
            "Treeview",
            background=[("selected", COR_ROXO_CLARO)],
            foreground=[("selected", COR_BRANCO)]
        )

        # ----------------------------------------------------
        # TÍTULO
        # ----------------------------------------------------

        titulo = tk.Label(
            root,
            text="Clínica Veterinária - Sistema CRUD",
            bg=COR_ROXO,
            fg=COR_BRANCO,
            font=("Arial", 16, "bold"),
            pady=12
        )

        titulo.pack(fill="x")

        # ----------------------------------------------------
        # ABAS
        # ----------------------------------------------------

        self.abas = ttk.Notebook(root)
        self.abas.pack(
            fill="both",
            expand=True,
            padx=12,
            pady=12
        )

        self.aba_clientes = ttk.Frame(
            self.abas,
            padding=10
        )

        self.aba_pets = ttk.Frame(
            self.abas,
            padding=10
        )

        self.aba_consultas = ttk.Frame(
            self.abas,
            padding=10
        )

        self.abas.add(
            self.aba_clientes,
            text="Clientes"
        )

        self.abas.add(
            self.aba_pets,
            text="Pets"
        )

        self.abas.add(
            self.aba_consultas,
            text="Consultas"
        )

        # ----------------------------------------------------
        # MONTA AS ABAS
        # ----------------------------------------------------

        self._montar_aba_clientes()
        self._montar_aba_pets()
        self._montar_aba_consultas()

        # ----------------------------------------------------
        # CARREGA OS DADOS
        # ----------------------------------------------------

        self.atualizar_todas_listas()

    # ========================================================
    # FUNÇÕES AUXILIARES
    # ========================================================

    def mostrar_erro(self, erro):
        messagebox.showerror(
            "Erro no banco de dados",
            str(erro),
            parent=self.root
        )

    def _criar_tabela(
        self,
        parent,
        colunas,
        larguras=None,
        altura=12
    ):

        frame = ttk.Frame(parent)

        frame.pack(
            fill="both",
            expand=True,
            pady=(10, 0)
        )

        tree = ttk.Treeview(
            frame,
            columns=colunas,
            show="headings",
            height=altura
        )

        barra_y = ttk.Scrollbar(
            frame,
            orient="vertical",
            command=tree.yview
        )

        barra_x = ttk.Scrollbar(
            frame,
            orient="horizontal",
            command=tree.xview
        )

        tree.configure(
            yscrollcommand=barra_y.set,
            xscrollcommand=barra_x.set
        )

        tree.grid(
            row=0,
            column=0,
            sticky="nsew"
        )

        barra_y.grid(
            row=0,
            column=1,
            sticky="ns"
        )

        barra_x.grid(
            row=1,
            column=0,
            sticky="ew"
        )

        frame.rowconfigure(
            0,
            weight=1
        )

        frame.columnconfigure(
            0,
            weight=1
        )

        for i, coluna in enumerate(colunas):

            tree.heading(
                coluna,
                text=coluna
            )

            largura = (
                larguras[i]
                if larguras
                else 130
            )

            tree.column(
                coluna,
                width=largura,
                minwidth=65,
                anchor="center"
            )

        return tree

    def _campo(
        self,
        parent,
        rotulo,
        linha,
        largura=34
    ):

        ttk.Label(
            parent,
            text=rotulo
        ).grid(
            row=linha,
            column=0,
            sticky="w",
            padx=(0, 8),
            pady=4
        )

        entrada = ttk.Entry(
            parent,
            width=largura
        )

        entrada.grid(
            row=linha,
            column=1,
            sticky="ew",
            pady=4
        )

        return entrada

    def _limpar(self, *entradas):

        for entrada in entradas:
            entrada.delete(
                0,
                tk.END
            )

    def atualizar_todas_listas(self):

        self.listar_clientes()
        self.listar_pets()
        self.listar_consultas()

    # ========================================================
    # CLIENTES
    # ========================================================

    def _montar_aba_clientes(self):

        form = ttk.LabelFrame(
            self.aba_clientes,
            text="Dados do cliente",
            padding=10
        )

        form.pack(fill="x")

        form.columnconfigure(
            1,
            weight=1
        )

        self.cliente_id = self._campo(
            form,
            "ID (para atualizar/excluir):",
            0
        )

        self.cliente_nome = self._campo(
            form,
            "Nome:",
            1
        )

        self.cliente_email = self._campo(
            form,
            "Email:",
            2
        )

        botoes = ttk.Frame(form)

        botoes.grid(
            row=0,
            column=2,
            rowspan=3,
            padx=(14, 0),
            sticky="ns"
        )

        ttk.Button(
            botoes,
            text="Cadastrar",
            command=self.cadastrar_cliente
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Atualizar",
            command=self.atualizar_cliente
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Excluir",
            command=self.excluir_cliente
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Limpar campos",
            command=lambda: self._limpar(
                self.cliente_id,
                self.cliente_nome,
                self.cliente_email
            )
        ).pack(fill="x", pady=2)

        self.tabela_clientes = self._criar_tabela(
            self.aba_clientes,
            ("ID", "Nome", "Email"),
            (70, 300, 320)
        )

        self.tabela_clientes.bind(
            "<<TreeviewSelect>>",
            self.selecionar_cliente
        )

    def listar_clientes(self):

        try:

            for item in self.tabela_clientes.get_children():
                self.tabela_clientes.delete(item)

            resposta = (
                supabase
                .table("clientes")
                .select("id, nome, email")
                .order("nome")
                .execute()
            )

            for cliente in resposta.data:

                self.tabela_clientes.insert(
                    "",
                    "end",
                    values=(
                        cliente.get("id"),
                        cliente.get("nome"),
                        cliente.get("email")
                    )
                )

        except Exception as erro:
            self.mostrar_erro(erro)

    def selecionar_cliente(self, _event=None):

        selecionado = self.tabela_clientes.selection()

        if selecionado:

            valores = self.tabela_clientes.item(
                selecionado[0],
                "values"
            )

            campos = (
                self.cliente_id,
                self.cliente_nome,
                self.cliente_email
            )

            self._limpar(*campos)

            for campo, valor in zip(
                campos,
                valores
            ):

                campo.insert(
                    0,
                    valor or ""
                )

    def cadastrar_cliente(self):

        nome = self.cliente_nome.get().strip()
        email = self.cliente_email.get().strip()

        if not nome:

            messagebox.showwarning(
                "Validação",
                "Informe o nome do cliente.",
                parent=self.root
            )

            return

        try:

            supabase.table("clientes").insert({
                "nome": nome,
                "email": email or None
            }).execute()

            messagebox.showinfo(
                "Concluído",
                "Cliente cadastrado.",
                parent=self.root
            )

            self.listar_clientes()

            self._limpar(
                self.cliente_id,
                self.cliente_nome,
                self.cliente_email
            )

        except Exception as erro:
            self.mostrar_erro(erro)

    def atualizar_cliente(self):

        try:

            id_cliente = int(
                self.cliente_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Informe um ID de cliente válido.",
                parent=self.root
            )

            return

        nome = self.cliente_nome.get().strip()
        email = self.cliente_email.get().strip()

        if not nome:

            messagebox.showwarning(
                "Validação",
                "Informe o nome do cliente.",
                parent=self.root
            )

            return

        try:

            resposta = (
                supabase
                .table("clientes")
                .update({
                    "nome": nome,
                    "email": email or None
                })
                .eq("id", id_cliente)
                .execute()
            )

            if resposta.data:

                messagebox.showinfo(
                    "Resultado",
                    "Cliente atualizado.",
                    parent=self.root
                )

            else:

                messagebox.showinfo(
                    "Resultado",
                    "Cliente não encontrado.",
                    parent=self.root
                )

            self.listar_clientes()

        except Exception as erro:
            self.mostrar_erro(erro)

    def excluir_cliente(self):

        try:

            id_cliente = int(
                self.cliente_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Selecione ou informe um ID de cliente.",
                parent=self.root
            )

            return

        confirmar = messagebox.askyesno(
            "Confirmar",
            "Excluir o cliente e seus pets e consultas relacionados?",
            parent=self.root
        )

        if not confirmar:
            return

        try:

            # Busca os pets desse cliente
            resposta_pets = (
                supabase
                .table("pet")
                .select("id")
                .eq("id_cliente", id_cliente)
                .execute()
            )

            pets = resposta_pets.data

            # Exclui as consultas dos pets
            for pet in pets:

                id_pet = pet["id"]

                (
                    supabase
                    .table("consultas")
                    .delete()
                    .eq("id_pet", id_pet)
                    .execute()
                )

            # Exclui os pets
            (
                supabase
                .table("pet")
                .delete()
                .eq("id_cliente", id_cliente)
                .execute()
            )

            # Exclui o cliente
            resposta = (
                supabase
                .table("clientes")
                .delete()
                .eq("id", id_cliente)
                .execute()
            )

            if resposta.data:

                messagebox.showinfo(
                    "Resultado",
                    "Cliente excluído.",
                    parent=self.root
                )

            else:

                messagebox.showinfo(
                    "Resultado",
                    "Cliente não encontrado.",
                    parent=self.root
                )

            self.listar_clientes()
            self.listar_pets()
            self.listar_consultas()

        except Exception as erro:
            self.mostrar_erro(erro)

    # ========================================================
    # PETS
    # ========================================================

    def _montar_aba_pets(self):

        form = ttk.LabelFrame(
            self.aba_pets,
            text="Dados do pet",
            padding=10
        )

        form.pack(fill="x")

        form.columnconfigure(
            1,
            weight=1
        )

        self.pet_id = self._campo(
            form,
            "ID (para atualizar/excluir):",
            0
        )

        self.pet_nome = self._campo(
            form,
            "Nome:",
            1
        )

        self.pet_especie = self._campo(
            form,
            "Espécie:",
            2
        )

        self.pet_cliente = self._campo(
            form,
            "ID do cliente dono:",
            3
        )

        botoes = ttk.Frame(form)

        botoes.grid(
            row=0,
            column=2,
            rowspan=4,
            padx=(14, 0),
            sticky="ns"
        )

        ttk.Button(
            botoes,
            text="Cadastrar",
            command=self.cadastrar_pet
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Atualizar",
            command=self.atualizar_pet
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Excluir",
            command=self.excluir_pet
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Total gasto do pet",
            command=self.total_gasto_pet
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Limpar campos",
            command=lambda: self._limpar(
                self.pet_id,
                self.pet_nome,
                self.pet_especie,
                self.pet_cliente
            )
        ).pack(fill="x", pady=2)

        self.tabela_pets = self._criar_tabela(
            self.aba_pets,
            ("ID", "Nome", "Espécie", "ID Cliente"),
            (70, 220, 180, 120)
        )

        self.tabela_pets.bind(
            "<<TreeviewSelect>>",
            self.selecionar_pet
        )

    def listar_pets(self):

        try:

            for item in self.tabela_pets.get_children():
                self.tabela_pets.delete(item)

            resposta = (
                supabase
                .table("pet")
                .select("id, nome, especie, id_cliente")
                .order("nome")
                .execute()
            )

            for pet in resposta.data:

                self.tabela_pets.insert(
                    "",
                    "end",
                    values=(
                        pet.get("id"),
                        pet.get("nome"),
                        pet.get("especie"),
                        pet.get("id_cliente")
                    )
                )

        except Exception as erro:
            self.mostrar_erro(erro)

    def selecionar_pet(self, _event=None):

        selecionado = self.tabela_pets.selection()

        if selecionado:

            valores = self.tabela_pets.item(
                selecionado[0],
                "values"
            )

            campos = (
                self.pet_id,
                self.pet_nome,
                self.pet_especie,
                self.pet_cliente
            )

            self._limpar(*campos)

            for campo, valor in zip(
                campos,
                valores
            ):

                campo.insert(
                    0,
                    valor or ""
                )

    def _validar_pet(self):

        nome = self.pet_nome.get().strip()
        especie = self.pet_especie.get().strip()

        try:

            id_cliente = int(
                self.pet_cliente.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Informe um ID de cliente válido.",
                parent=self.root
            )

            return None

        if not nome or not especie:

            messagebox.showwarning(
                "Validação",
                "Preencha o nome e a espécie.",
                parent=self.root
            )

            return None

        return nome, especie, id_cliente

    def cadastrar_pet(self):

        dados = self._validar_pet()

        if not dados:
            return

        nome, especie, id_cliente = dados

        try:

            supabase.table("pet").insert({
                "nome": nome,
                "especie": especie,
                "id_cliente": id_cliente
            }).execute()

            messagebox.showinfo(
                "Concluído",
                "Pet cadastrado.",
                parent=self.root
            )

            self.listar_pets()

            self._limpar(
                self.pet_id,
                self.pet_nome,
                self.pet_especie,
                self.pet_cliente
            )

        except Exception as erro:
            self.mostrar_erro(erro)

    def atualizar_pet(self):

        dados = self._validar_pet()

        if not dados:
            return

        try:

            id_pet = int(
                self.pet_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Informe um ID de pet válido.",
                parent=self.root
            )

            return

        nome, especie, id_cliente = dados

        try:

            resposta = (
                supabase
                .table("pet")
                .update({
                    "nome": nome,
                    "especie": especie,
                    "id_cliente": id_cliente
                })
                .eq("id", id_pet)
                .execute()
            )

            messagebox.showinfo(
                "Resultado",
                "Pet atualizado."
                if resposta.data
                else "Pet não encontrado.",
                parent=self.root
            )

            self.listar_pets()

        except Exception as erro:
            self.mostrar_erro(erro)

    def excluir_pet(self):

        try:

            id_pet = int(
                self.pet_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Selecione ou informe um ID de pet.",
                parent=self.root
            )

            return

        confirmar = messagebox.askyesno(
            "Confirmar",
            "Excluir este pet e suas consultas?",
            parent=self.root
        )

        if not confirmar:
            return

        try:

            (
                supabase
                .table("consultas")
                .delete()
                .eq("id_pet", id_pet)
                .execute()
            )

            resposta = (
                supabase
                .table("pet")
                .delete()
                .eq("id", id_pet)
                .execute()
            )

            messagebox.showinfo(
                "Resultado",
                "Pet excluído."
                if resposta.data
                else "Pet não encontrado.",
                parent=self.root
            )

            self.listar_pets()
            self.listar_consultas()

        except Exception as erro:
            self.mostrar_erro(erro)

    def total_gasto_pet(self):

        try:

            id_pet = int(
                self.pet_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Selecione um pet ou informe o ID dele.",
                parent=self.root
            )

            return

        try:

            resposta = supabase.rpc(
                "fn_total_gasto_pet",
                {
                    "id_pet_param": id_pet
                }
            ).execute()

            valor = resposta.data

            if valor is None:
                valor = 0

            messagebox.showinfo(
                "Total das consultas",
                "Total registrado para o pet: R$ {:.2f}".format(
                    float(valor)
                ),
                parent=self.root
            )

        except Exception as erro:
            self.mostrar_erro(erro)

    # ========================================================
    # CONSULTAS
    # ========================================================

    def _montar_aba_consultas(self):

        form = ttk.LabelFrame(
            self.aba_consultas,
            text="Dados da consulta",
            padding=10
        )

        form.pack(fill="x")

        form.columnconfigure(
            1,
            weight=1
        )

        self.consulta_id = self._campo(
            form,
            "ID (para atualizar/excluir):",
            0
        )

        self.consulta_data = self._campo(
            form,
            "Data (AAAA-MM-DD):",
            1
        )

        self.consulta_descricao = self._campo(
            form,
            "Descrição:",
            2
        )

        self.consulta_valor = self._campo(
            form,
            "Valor:",
            3
        )

        self.consulta_pet = self._campo(
            form,
            "ID do pet:",
            4
        )

        botoes = ttk.Frame(form)

        botoes.grid(
            row=0,
            column=2,
            rowspan=5,
            padx=(14, 0),
            sticky="ns"
        )

        ttk.Button(
            botoes,
            text="Registrar consulta",
            command=self.cadastrar_consulta
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Atualizar",
            command=self.atualizar_consulta
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Excluir",
            command=self.excluir_consulta
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Atualizar lista",
            command=self.listar_consultas
        ).pack(fill="x", pady=2)

        ttk.Button(
            botoes,
            text="Limpar campos",
            command=lambda: self._limpar(
                self.consulta_id,
                self.consulta_data,
                self.consulta_descricao,
                self.consulta_valor,
                self.consulta_pet
            )
        ).pack(fill="x", pady=2)

        ttk.Label(
            self.aba_consultas,
            text="A listagem abaixo é carregada da View vw_consultas_detalhadas."
        ).pack(
            anchor="w",
            pady=(8, 0)
        )

        self.tabela_consultas = self._criar_tabela(
            self.aba_consultas,
            (
                "ID",
                "Data",
                "Descrição",
                "Valor",
                "ID Pet",
                "Pet",
                "Espécie",
                "Cliente",
                "ID Cliente"
            ),
            (
                55,
                100,
                170,
                90,
                65,
                120,
                100,
                160,
                75
            ),
            altura=11
        )

        self.tabela_consultas.bind(
            "<<TreeviewSelect>>",
            self.selecionar_consulta
        )

    def listar_consultas(self):

        try:

            for item in self.tabela_consultas.get_children():
                self.tabela_consultas.delete(item)

            resposta = (
                supabase
                .table("vw_consultas_detalhadas")
                .select(
                    "id_consulta, data, descricao, valor, "
                    "id_pet, nome_pet, especie, "
                    "nome_cliente, id_cliente"
                )
                .order(
                    "data",
                    desc=True
                )
                .execute()
            )

            for consulta in resposta.data:

                self.tabela_consultas.insert(
                    "",
                    "end",
                    values=(
                        consulta.get("id_consulta"),
                        consulta.get("data"),
                        consulta.get("descricao"),
                        consulta.get("valor"),
                        consulta.get("id_pet"),
                        consulta.get("nome_pet"),
                        consulta.get("especie"),
                        consulta.get("nome_cliente"),
                        consulta.get("id_cliente")
                    )
                )

        except Exception as erro:
            self.mostrar_erro(erro)

    def selecionar_consulta(self, _event=None):

        selecionado = self.tabela_consultas.selection()

        if selecionado:

            valores = self.tabela_consultas.item(
                selecionado[0],
                "values"
            )

            campos = (
                self.consulta_id,
                self.consulta_data,
                self.consulta_descricao,
                self.consulta_valor,
                self.consulta_pet
            )

            dados = (
                valores[0],
                valores[1],
                valores[2],
                valores[3],
                valores[4]
            )

            self._limpar(*campos)

            for campo, valor in zip(
                campos,
                dados
            ):

                campo.insert(
                    0,
                    valor or ""
                )

    def _validar_consulta(self):

        data = self.consulta_data.get().strip()
        descricao = self.consulta_descricao.get().strip()

        try:

            valor = Decimal(
                self.consulta_valor
                .get()
                .replace(",", ".")
            )

            id_pet = int(
                self.consulta_pet.get()
            )

        except (InvalidOperation, ValueError):

            messagebox.showwarning(
                "Validação",
                "Informe um valor numérico e um ID de pet inteiro.",
                parent=self.root
            )

            return None

        if not data or not descricao or valor < 0:

            messagebox.showwarning(
                "Validação",
                "Informe data, descrição e valor maior ou igual a zero.",
                parent=self.root
            )

            return None

        return data, descricao, valor, id_pet

    def cadastrar_consulta(self):

        dados = self._validar_consulta()

        if not dados:
            return

        data, descricao, valor, id_pet = dados

        try:

            # Chama uma FUNCTION do PostgreSQL através do Supabase RPC.
            resposta = supabase.rpc(
                "fn_registrar_consulta",
                {
                    "p_data": data,
                    "p_descricao": descricao,
                    "p_valor": float(valor),
                    "p_id_pet": id_pet
                }
            ).execute()

            messagebox.showinfo(
                "Concluído",
                "Consulta registrada.",
                parent=self.root
            )

            self.listar_consultas()

            self._limpar(
                self.consulta_id,
                self.consulta_data,
                self.consulta_descricao,
                self.consulta_valor,
                self.consulta_pet
            )

        except Exception as erro:
            self.mostrar_erro(erro)

    def atualizar_consulta(self):

        try:

            id_consulta = int(
                self.consulta_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Selecione ou informe o ID da consulta.",
                parent=self.root
            )

            return

        dados = self._validar_consulta()

        if not dados:
            return

        data, descricao, valor, id_pet = dados

        try:

            resposta = (
                supabase
                .table("consultas")
                .update({
                    "data": data,
                    "descricao": descricao,
                    "valor": float(valor),
                    "id_pet": id_pet
                })
                .eq("id", id_consulta)
                .execute()
            )

            messagebox.showinfo(
                "Resultado",
                "Consulta atualizada."
                if resposta.data
                else "Consulta não encontrada.",
                parent=self.root
            )

            self.listar_consultas()

        except Exception as erro:
            self.mostrar_erro(erro)

    def excluir_consulta(self):

        try:

            id_consulta = int(
                self.consulta_id.get()
            )

        except ValueError:

            messagebox.showwarning(
                "Validação",
                "Selecione ou informe o ID da consulta.",
                parent=self.root
            )

            return

        confirmar = messagebox.askyesno(
            "Confirmar",
            "Excluir esta consulta?",
            parent=self.root
        )

        if not confirmar:
            return

        try:

            resposta = (
                supabase
                .table("consultas")
                .delete()
                .eq("id", id_consulta)
                .execute()
            )

            messagebox.showinfo(
                "Resultado",
                "Consulta excluída."
                if resposta.data
                else "Consulta não encontrada.",
                parent=self.root
            )

            self.listar_consultas()

        except Exception as erro:
            self.mostrar_erro(erro)

# ============================================================
# INICIAR SISTEMA
# ============================================================

if __name__ == "__main__":
    janela = tk.Tk()
    app = ClinicaApp(janela)
    janela.mainloop()
