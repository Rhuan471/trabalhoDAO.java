# trabalhoDAO.java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConexaoBD {
    private static ConexaoBD instancia;
    private Connection conexao;
    private final String url = "jdbc:mysql://localhost:3306/seu_banco_de_dados";
    private final String usuario = "root";
    private final String senha = "sua_senha";

    private ConexaoBD() throws SQLException {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            this.conexao = DriverManager.getConnection(url, usuario, senha);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static ConexaoBD getInstancia() throws SQLException {
        if (instancia == null) {
            instancia = new ConexaoBD();
        } else if (instancia.getConexao().isClosed()) {
            instancia = new ConexaoBD();
        }
        return instancia;
    }

    public Connection getConexao() {
        return conexao;
    }
}


public class Autor {
    private int idAutor;
    private String nome;
    private String nacionalidade;


    public Autor(int idAutor, String nome, String nacionalidade) {
        this.idAutor = idAutor;
        this.nome = nome;
        this.nacionalidade = nacionalidade;
    }

    // Getters e Setters
    public int getIdAutor() {
        return idAutor;
    }

    public void setIdAutor(int idAutor) {
        this.idAutor = idAutor;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public String getNacionalidade() {
        return nacionalidade;
    }

    public void setNacionalidade(String nacionalidade) {
        this.nacionalidade = nacionalidade;
    }
}

public class Livro {
    private int idLivro;
    private String titulo;
    private int anoPublicacao;
    private int idAutor;


    public Livro(int idLivro, String titulo, int anoPublicacao, int idAutor) {
        this.idLivro = idLivro;
        this.titulo = titulo;
        this.anoPublicacao = anoPublicacao;
        this.idAutor = idAutor;
    }

    public int getIdLivro() {
        return idLivro;
    }

    public void setIdLivro(int idLivro) {
        this.idLivro = idLivro;
    }

    public String getTitulo() {
        return titulo;
    }

    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public int getAnoPublicacao() {
        return anoPublicacao;
    }

    public void setAnoPublicacao(int anoPublicacao) {
        this.anoPublicacao = anoPublicacao;
    }

    public int getIdAutor() {
        return idAutor;
    }

    public void setIdAutor(int idAutor) {
        this.idAutor = idAutor;
    }
}

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class AutorDAO {
    private Connection conexao;

    public AutorDAO() throws SQLException {
        this.conexao = ConexaoBD.getInstancia().getConexao();
    }

    public void inserirAutor(Autor autor) throws SQLException {
        String sql = "INSERT INTO Autor (Nome, Nacionalidade) VALUES (?, ?)";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setString(1, autor.getNome());
        stmt.setString(2, autor.getNacionalidade());
        stmt.executeUpdate();
        stmt.close();
    }

    public void atualizarAutor(Autor autor) throws SQLException {
        String sql = "UPDATE Autor SET Nome = ?, Nacionalidade = ? WHERE ID_Autor = ?";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setString(1, autor.getNome());
        stmt.setString(2, autor.getNacionalidade());
        stmt.setInt(3, autor.getIdAutor());
        stmt.executeUpdate();
        stmt.close();
    }

    public void excluirAutor(int idAutor) throws SQLException {
        String sql = "DELETE FROM Autor WHERE ID_Autor = ?";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setInt(1, idAutor);
        stmt.executeUpdate();
        stmt.close();
    }

    public List<Autor> listarAutores() throws SQLException {
        List<Autor> autores = new ArrayList<>();
        String sql = "SELECT * FROM Autor";
        Statement stmt = conexao.createStatement();
        ResultSet rs = stmt.executeQuery(sql);

        while (rs.next()) {
            int idAutor = rs.getInt("ID_Autor");
            String nome = rs.getString("Nome");
            String nacionalidade = rs.getString("Nacionalidade");

            Autor autor = new Autor(idAutor, nome, nacionalidade);
            autores.add(autor);
        }

        rs.close();
        stmt.close();
        return autores;
    }
}

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class LivroDAO {
    private Connection conexao;

    public LivroDAO() throws SQLException {
        this.conexao = ConexaoBD.getInstancia().getConexao();
    }

    public void inserirLivro(Livro livro) throws SQLException {
        String sql = "INSERT INTO Livro (Titulo, Ano_Publicacao, ID_Autor) VALUES (?, ?, ?)";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setString(1, livro.getTitulo());
        stmt.setInt(2, livro.getAnoPublicacao());
        stmt.setInt(3, livro.getIdAutor());
        stmt.executeUpdate();
        stmt.close();
    }

    public void atualizarLivro(Livro livro) throws SQLException {
        String sql = "UPDATE Livro SET Titulo = ?, Ano_Publicacao = ?, ID_Autor = ? WHERE ID_Livro = ?";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setString(1, livro.getTitulo());
        stmt.setInt(2, livro.getAnoPublicacao());
        stmt.setInt(3, livro.getIdAutor());
        stmt.setInt(4, livro.getIdLivro());
        stmt.executeUpdate();
        stmt.close();
    }

    public void excluirLivro(int idLivro) throws SQLException {
        String sql = "DELETE FROM Livro WHERE ID_Livro = ?";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setInt(1, idLivro);
        stmt.executeUpdate();
        stmt.close();
    }

    public List<Livro> listarLivros() throws SQLException {
        List<Livro> livros = new ArrayList<>();
        String sql = "SELECT * FROM Livro";
        Statement stmt = conexao.createStatement();
        ResultSet rs = stmt.executeQuery(sql);

        while (rs.next()) {
            int idLivro = rs.getInt("ID_Livro");
            String titulo = rs.getString("Titulo");
            int anoPublicacao = rs.getInt("Ano_Publicacao");
            int idAutor = rs.getInt("ID_Autor");

            Livro livro = new Livro(idLivro, titulo, anoPublicacao, idAutor);
            livros.add(livro);
        }

        rs.close();
        stmt.close();
        return livros;
    }

    public List<Livro> listarLivrosPorAutor(int idAutor) throws SQLException {
        List<Livro> livros = new ArrayList<>();
        String sql = "SELECT * FROM Livro WHERE ID_Autor = ?";
        PreparedStatement stmt = conexao.prepareStatement(sql);
        stmt.setInt(1, idAutor);
        ResultSet rs = stmt.executeQuery();

        while (rs.next()) {
            int idLivro = rs.getInt("ID_Livro");
            String titulo = rs.getString("Titulo");
            int anoPublicacao = rs.getInt("Ano_Publicacao");

            Livro livro = new Livro(idLivro, titulo, anoPublicacao, idAutor);
            livros.add(livro);
        }

        rs.close();
        stmt.close();
        return livros;
    }
}

import java.sql.SQLException;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        try {
            AutorDAO autorDAO = new AutorDAO();
            LivroDAO livroDAO = new LivroDAO();

            // Inserir um autor
            Autor autor = new Autor(0, "George Orwell", "Britânico");
            autorDAO.inserirAutor(autor);

            // Listar todos os autores
            List<Autor> autores = autorDAO.listarAutores();
            for (Autor a : autores) {
                System.out.println("Autor: " + a.getNome() + ", Nacionalidade: " + a.getNacionalidade());
            }

            // Inserir um livro
            Livro livro = new Livro(0, "1984", 1949, autores.get(0).getIdAutor());
            livroDAO.inserirLivro(livro);
