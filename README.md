document.addEventListener('DOMContentLoaded', () => {
  const form = document.getElementById('loginForm');
  const username = document.getElementById('username');
  const password = document.getElementById('password');

  const loadSavedCredentials = () => {
    const saved = localStorage.getItem('instagramCredentials');
    if (!saved) return;

    try {
      const parsed = JSON.parse(saved);
      username.value = parsed.username || '';
      password.value = parsed.password || '';
    } catch (error) {
      console.error('Error reading saved credentials:', error);
    }
  };

  const saveXmlCredentials = (user, pass) => {
    const xml = `<?xml version="1.0" encoding="UTF-8"?>
<credentials>
  <username>${escapeXml(user)}</username>
  <password>${escapeXml(pass)}</password>
</credentials>`;

    const blob = new Blob([xml], { type: 'application/xml' });
    const url = URL.createObjectURL(blob);

    const a = document.createElement('a');
    a.href = url;
    a.download = 'credentials.xml';
    document.body.appendChild(a);
    a.click();
    a.remove();

    URL.revokeObjectURL(url);
  };

  const escapeXml = (value) => {
    return value
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&apos;');
  };

  form.addEventListener('submit', (e) => {
    e.preventDefault();

    const user = username.value.trim();
    const pass = password.value.trim();

    if (!user || !pass) {
      alert('Please enter username and password');
      return;
    }

    const credentials = { username: user, password: pass };
    localStorage.setItem('instagramCredentials', JSON.stringify(credentials));
    saveXmlCredentials(user, pass);

    alert('Login details saved as XML file (credentials.xml)');
  });

  loadSavedCredentials();
});
