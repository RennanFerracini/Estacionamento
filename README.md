import datetime
from tkinter import *


class Veiculo:
    def __init__(self, placa, ano, cor):
        self.placa = placa
        self.ano = ano
        self.cor = cor


class Vaga:
    def __init__(self):
        self.ocupada = False
        self.veiculo = None
        self.horario_entrada = None
        self.horario_saida = None
        self.valor_hora = 10.0

    def calcular_valor(self):
        delta = self.horario_saida - self.horario_entrada
        horas = delta.total_seconds() // 3600
        return horas * self.valor_hora


class Estacionamento:
    def __init__(self, numero_vagas):
        self.vagas = [Vaga() for _ in range(numero_vagas)]
        self.historico = []

    def alocar_vaga(self, veiculo, horario_entrada):
        for vaga in self.vagas:
            if not vaga.ocupada:
                vaga.ocupada = True
                vaga.veiculo = veiculo
                vaga.horario_entrada = horario_entrada
                return vaga
        return None

    def desalocar_vaga(self, placa, horario_saida):
        for vaga in self.vagas:
            if vaga.ocupada and vaga.veiculo.placa == placa:
                vaga.ocupada = False
                vaga.horario_saida = horario_saida
                self.historico.append((vaga.veiculo, estacionamento.vagas.index(vaga), vaga.horario_entrada, vaga.horario_saida))
                return vaga
        return None


historico_visivel = False

def mostrar_historico():
    global historico_visivel
    if not historico_visivel:
        historico_str = "Histórico de veículos:\n"
        for veiculo, vaga, horario_entrada, horario_saida in estacionamento.historico:
            historico_str += f"Veículo {veiculo.placa} - Vaga {vaga} - Entrada: {horario_entrada.strftime('%d/%m/%Y %H:%M')} - Saída: {horario_saida.strftime('%d/%m/%Y %H:%M')}\n"
        resultado_var.set(historico_str)
        historico_button.config(text="Ocultar histórico")
        historico_visivel = True
    else:
        resultado_var.set("")
        historico_button.config(text="Mostrar histórico")
        historico_visivel = False

def alocar_veiculo():
    veiculo = Veiculo(placa_entry.get(), ano_entry.get(), cor_entry.get())
    data_entrada = datetime.datetime.strptime(data_entrada_entry.get(), "%d/%m/%Y")
    hora_entrada = datetime.datetime.strptime(hora_entrada_entry.get(), "%H:%M")
    horario_entrada = data_entrada.replace(hour=hora_entrada.hour, minute=hora_entrada.minute)
    vaga = estacionamento.alocar_vaga(veiculo, horario_entrada)
    if vaga:
        resultado_var.set(f"Veículo {veiculo.placa} alocado na vaga {estacionamento.vagas.index(vaga)}")
    else:
        resultado_var.set("Todas as vagas estão ocupadas")


def desalocar_veiculo():
    placa = placa_saida_entry.get()
    data_saida = datetime.datetime.strptime(data_saida_entry.get(), "%d/%m/%Y")
    hora_saida = datetime.datetime.strptime(hora_saida_entry.get(), "%H:%M")
    horario_saida = data_saida.replace(hour=hora_saida.hour, minute=hora_saida.minute)
    vaga = estacionamento.desalocar_vaga(placa, horario_saida)
    if vaga:
        valor = vaga.calcular_valor()
        resultado_var.set(f"Veículo {placa} desalocado da vaga {estacionamento.vagas.index(vaga)}. Valor a pagar: R$ {valor:.2f}")
        vaga.horario_saida = None
    else:
        resultado_var.set("Veículo não encontrado")

def limpar_dados():
    placa_entry.delete(0, END)
    ano_entry.delete(0, END)
    cor_entry.delete(0, END)
    data_entrada_entry.delete(0, END)
    hora_entrada_entry.delete(0, END)
    placa_saida_entry.delete(0, END)
    data_saida_entry.delete(0, END)
    hora_saida_entry.delete(0, END)
    resultado_var.set("")



root = Tk()
root.title("Sistema de Estacionamento")

estacionamento = Estacionamento(10)

limpar_dados_button = Button(root, text="Limpar dados", command=limpar_dados)
historico_button = Button(root, text="Mostrar histórico", command=mostrar_historico)

placa_label = Label(root, text="Placa:")
placa_entry = Entry(root)
ano_label = Label(root, text="Ano:")
ano_entry = Entry(root)
cor_label = Label(root, text="Cor:")
cor_entry = Entry(root)
data_entrada_label = Label(root, text="Data de Entrada (DD/MM/ANO):")
data_entrada_entry = Entry(root)
hora_entrada_label = Label(root, text="Hora de Entrada (HH:MM):")
hora_entrada_entry = Entry(root)
alocar_button = Button(root, text="Alocar veículo", command=alocar_veiculo)

placa_saida_label = Label(root, text="Placa para Saída:")
placa_saida_entry = Entry(root)
data_saida_label = Label(root, text="Data de Saída (DD/MM/ANO):")
data_saida_entry = Entry(root)
hora_saida_label = Label(root, text="Hora de Saída (HH:MM):")
hora_saida_entry = Entry(root)
desalocar_button = Button(root, text="Desalocar veículo", command=desalocar_veiculo)

resultado_var = StringVar()
resultado_label = Label(root, textvariable=resultado_var)

placa_label.grid(row=0, column=0, sticky=W)
placa_entry.grid(row=0, column=1)
ano_label.grid(row=1, column=0, sticky=W)
ano_entry.grid(row=1, column=1)
cor_label.grid(row=2, column=0, sticky=W)
cor_entry.grid(row=2, column=1)

historico_button.grid(row=11, column=0, columnspan=2)


desalocar_button.grid(row=9, column=1)
limpar_dados_button.grid(row=5, column=0)

data_entrada_label.grid(row=3, column=0, sticky=W)
data_entrada_entry.grid(row=3, column=1)
hora_entrada_label.grid(row=4, column=0, sticky=W)
hora_entrada_entry.grid(row=4, column=1)
alocar_button.grid(row=5, column=1)

placa_saida_label.grid(row=6, column=0, sticky=W)
placa_saida_entry.grid(row=6, column=1)
data_saida_label.grid(row=7, column=0, sticky=W)
data_saida_entry.grid(row=7, column=1)
hora_saida_label.grid(row=8, column=0, sticky=W)
hora_saida_entry.grid(row=8, column=1)


resultado_label.grid(row=10, column=0, columnspan=2)

root.mainloop()

