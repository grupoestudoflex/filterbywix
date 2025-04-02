# 🚀 Projeto: Filtro de Cursos no Wix

## 📌 Descrição
Este projeto implementa um **mecanismo de busca de cursos** em um site Wix, utilizando a API Velo para interações dinâmicas com a coleção de cursos. A pesquisa permite encontrar cursos em tempo real, **ignorando acentos**, exibindo os resultados dinamicamente conforme o usuário digita.

## 🛠️ Tecnologias Utilizadas
- **Wix Velo** (JavaScript no backend)
- **Wix Data API** (para manipular a coleção de cursos)
- **Wix Repeater** (para exibir os cursos encontrados)

---

## 📂 Estrutura da Coleção `Cursos`
A coleção de cursos é organizada da seguinte forma:

| Campo          | Tipo   | Descrição                                      |
|---------------|--------|------------------------------------------------|
| `title`       | Texto  | Nome do curso com acentuação                   |
| `titleNoAccent` | Texto  | Nome do curso sem acentuação (para pesquisa)   |
| `image`       | Imagem | URL da imagem do curso                         |
| `urlCurso`    | URL    | Link para acessar a página do curso            |

**Exemplo:**
| title        | titleNoAccent  | image        | urlCurso          |
|-------------|--------------|-------------|----------------|
| Elétrica    | Eletrica     | curso1.jpg  | /curso-eletrica |
| Programação | Programacao  | curso2.jpg  | /curso-prog     |
| Matemática  | Matematica   | curso3.jpg  | /curso-mat      |
| Python      | Python      | curso4.jpg  | /curso-python   |
| Java        | Java        | curso5.jpg  | /curso-java     |

🔹 Se o curso **não tem acento**, `titleNoAccent` é igual ao `title`.

---

## 🎯 Funcionalidades Implementadas
✅ Pesquisa dinâmica enquanto o usuário digita
✅ Busca que ignora acentos (`Elétrica` e `Eletrica` retornam o mesmo resultado)
✅ Exibição de todos os cursos caso o campo de busca esteja vazio
✅ Oculta a mensagem "Nenhum curso encontrado" quando há resultados
✅ Carrega até 100 cursos automaticamente

---

## 🔧 Configuração e Código
### **1️⃣ Adicionando Elementos na Página**
Adicione os seguintes elementos na página do Wix:
- **Input de busca** → ID: `#searchInput`
- **Botão de busca** → ID: `#searchButton`
- **Repeater de resultados** → ID: `#resultsRepeater`
- **Mensagem de "Nenhum curso encontrado"** → ID: `#noResultsMessage`

### **2️⃣ Código do Filtro de Cursos**
Adicione este código no **Velo do Wix**:

```javascript
import wixData from 'wix-data';

$w.onReady(function () {
    carregarCursos(); // Carrega todos os cursos ao iniciar a página

    // Evento para filtrar enquanto digita
    $w("#searchInput").onInput(() => {
        executarPesquisa(); // Chama a função sempre que o usuário digita
    });

    // Função que busca os cursos com ou sem filtro
    function executarPesquisa() {
        let query = $w("#searchInput").value.trim(); // Obtém o valor digitado

        if (query) {
            buscarCursos(query); // Filtra os cursos pelo título
        } else {
            carregarCursos(); // Se estiver vazio, carrega todos os cursos
        }
    }

    // Função para buscar cursos filtrados (ignora acentos)
    function buscarCursos(query) {
        let queryNoAccent = removerAcentos(query);
        
        wixData.query("Cursos")
            .contains("titleNoAccent", queryNoAccent)
            .find()
            .then((results) => {
                if (results.items.length > 0) {
                    exibirCursos(results.items);
                } else {
                    $w("#resultsRepeater").hide();
                    $w("#noResultsMessage").show(); // Mostra mensagem de "Nenhum curso encontrado"
                }
            })
            .catch((error) => console.log(error));
    }

    // Função para carregar todos os cursos
    function carregarCursos() {
        wixData.query("Cursos")
            .limit(100) // Carrega até 100 cursos
            .find()
            .then((results) => {
                if (results.items.length > 0) {
                    exibirCursos(results.items);
                } else {
                    $w("#resultsRepeater").hide();
                    $w("#noResultsMessage").show();
                }
            })
            .catch((error) => console.log(error));
    }

    // Função para exibir cursos no repeater
    function exibirCursos(items) {
        $w("#resultsRepeater").data = items;
        $w("#resultsRepeater").show();
        $w("#noResultsMessage").hide();

        $w("#resultsRepeater").onItemReady(($item, itemData) => {
            $item("#courseTitle").text = itemData.title;
            $item("#imageCourse").src = itemData.image;
            $item("#imageCourse").link = itemData.urlCurso;
        });
    }

    // Função para remover acentos de strings
    function removerAcentos(str) {
        return str.normalize("NFD").replace(/[̀-ͯ]/g, "");
    }
});
```

---

## 📢 Considerações Finais
Agora, seu mecanismo de busca:
✔️ Exibe todos os cursos ao carregar a página
✔️ Filtra os cursos enquanto o usuário digita
✔️ **Ignora acentos**, garantindo melhor experiência na pesquisa
✔️ Mostra até 100 cursos corretamente

