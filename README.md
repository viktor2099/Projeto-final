import React, { useState } from "react";
import { View, Text, TextInput, Button, FlatList, TouchableOpacity, StyleSheet } from "react-native";

const Produto = ({ item, remover, incrementar, decrementar }) => (
  <View style={styles.item}>
    <View>
      <Text style={styles.itemText}>{item.nome}</Text>
      <Text style={styles.precoText}>
        Preço unitário: R$ {item.preco.toFixed(2)} | Subtotal: R$ {(item.preco * item.qtd).toFixed(2)}
      </Text>
    </View>
    <View style={styles.controle}>
      <Button title="➖" onPress={() => decrementar(item)} />
      <Text style={styles.quantidade}>{item.qtd}</Text>
      <Button title="➕" onPress={() => incrementar(item)} />
      <TouchableOpacity onPress={() => remover(item)}>
        <Text style={styles.removeText}>❌</Text>
      </TouchableOpacity>
    </View>
  </View>
);

export default function App() {
  const [produtos, setProdutos] = useState([
    { nome: "Arroz", preco: 5.5, qtd: 1 },
    { nome: "Feijão", preco: 8.75, qtd: 1 },
    { nome: "Coca-Cola", preco: 5.50, qtd: 1 },
  ]);

  const [novoNome, setNovoNome] = useState("");
  const [novoPreco, setNovoPreco] = useState("");

  const adicionar = () => {
    if (novoNome.trim() !== "" && !isNaN(parseFloat(novoPreco))) {
      setProdutos([...produtos, { nome: novoNome, preco: parseFloat(novoPreco), qtd: 1 }]);
      setNovoNome("");
      setNovoPreco("");
    }
  };

  const remover = (item) => {
    setProdutos(produtos.filter((p) => p !== item));
  };

  const incrementar = (item) => {
    setProdutos(
      produtos.map((p) => (p === item ? { ...p, qtd: p.qtd + 1 } : p))
    );
  };

  const decrementar = (item) => {
    if (item.qtd > 1) {
      setProdutos(
        produtos.map((p) => (p === item ? { ...p, qtd: p.qtd - 1 } : p))
      );
    } else {
      remover(item);
    }
  };

  const total = produtos.reduce((acc, p) => acc + p.preco * p.qtd, 0);

  return (
    <View style={styles.container}>
      <Text style={styles.titulo}>🏬 Mercado do ouro</Text>
      <Text style={styles.subtitulo}>⭐ Produto do dia: Coca-Cola</Text>
      <Text style={styles.listaTitulo}>📋 Carrinho:</Text>

      {produtos.length === 0 ? (
        <Text style={styles.vazio}>Nenhum produto no carrinho 😢</Text>
      ) : (
        <FlatList
          data={produtos}
          keyExtractor={(item, index) => index.toString()}
          renderItem={({ item }) => (
            <Produto
              item={item}
              remover={remover}
              incrementar={incrementar}
              decrementar={decrementar}
            />
          )}
        />
      )}

      <Text style={styles.total}>💰 Total do carrinho: R$ {total.toFixed(2)}</Text>

      <TextInput
        style={styles.input}
        placeholder="Nome do produto"
        value={novoNome}
        onChangeText={setNovoNome}
      />
      <TextInput
        style={styles.input}
        placeholder="Preço do produto"
        value={novoPreco}
        onChangeText={setNovoPreco}
        keyboardType="numeric"
      />
      <Button title="➕ Adicionar Produto" onPress={adicionar} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 20, backgroundColor: "#F5F5F5" },
  titulo: { fontSize: 20, fontWeight: "bold", marginBottom: 5 },
  subtitulo: { fontSize: 18, marginBottom: 2 },
  listaTitulo: { fontSize: 18, marginVertical: 10, fontWeight: "600" },
  item: {
    flexDirection: "row",
    justifyContent: "space-between",
    padding: 0,
    marginVertical: 5,
    backgroundColor: "#FFF",
    borderRadius: 8,
    elevation: 2,
  },
  itemText: { fontSize: 15 },
  precoText: { fontSize: 10, color: "#555" },
  removeText: { color: "red", fontWeight: "bold", marginLeft: 5, fontSize: 16 },
  controle: { flexDirection: "row", alignItems: "center" },
  quantidade: { marginHorizontal: 10, fontSize: 11 },
  vazio: { fontSize: 16, fontStyle: "italic", color: "#555", marginBottom: 10 },
  input: {
    borderWidth: 1,
    borderColor: "#CCC",
    borderRadius: 8,
    padding: 10,
    marginVertical: 5,
    backgroundColor: "#FFF",
  },
  total: { fontSize: 18, fontWeight: "bold", marginVertical: 10 },
});
