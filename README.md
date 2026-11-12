<script type="module">
  import { createClient } from "https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/+esm";

  const supabase = createClient(
    "SUA_PROJECT_URL",
    "SUA_CHAVE_PUBLICAVEL"
  );

  const overlay = document.querySelector("#auth-overlay");
  const form = document.querySelector("#auth-form");
  const message = document.querySelector("#auth-message");
  let mode = "login";

  function openAuth(newMode) {
    mode = newMode;
    const cadastro = mode === "register";

    document.querySelector("#auth-title").textContent =
      cadastro ? "Criar sua conta" : "Entrar na conta";
    document.querySelector("#auth-description").textContent =
      cadastro ? "Cadastre-se para continuar." : "Acesse sua conta para continuar.";
    document.querySelector("#auth-submit").textContent =
      cadastro ? "Cadastrar-se" : "Entrar";

    message.textContent = "";
    overlay.hidden = false;
  }

  document.querySelector("#login-button")
    .addEventListener("click", () => openAuth("login"));

  document.querySelector("#register-button")
    .addEventListener("click", () => openAuth("register"));

  document.querySelector("#close-auth").addEventListener("click", () => {
    overlay.hidden = true;
  });

  form.addEventListener("submit", async (event) => {
    event.preventDefault();

    const email = document.querySelector("#auth-email").value.trim();
    const password = document.querySelector("#auth-password").value;
    message.textContent = "Processando…";

    const result = mode === "register"
      ? await supabase.auth.signUp({ email, password })
      : await supabase.auth.signInWithPassword({ email, password });

    if (result.error) {
      message.textContent = result.error.message;
      return;
    }

    message.textContent = mode === "register"
      ? "Cadastro enviado! Confira seu e-mail, se a confirmação estiver ativada."
      : "Login realizado com sucesso!";

    if (mode === "login") {
      setTimeout(() => { overlay.hidden = true; }, 1000);
    }
  });
</script>
