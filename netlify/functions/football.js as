exports.handler = async (event) => {
  const API_KEY = process.env.FOOTBALL_API_KEY || 'e732c7831eaa4cc9b12279fc1f636277';
  const { leagueId, type } = event.queryStringParameters || {};

  if (!leagueId) {
    return { statusCode: 400, body: JSON.stringify({ error: 'Missing leagueId' }) };
  }

  const base = 'https://api.football-data.org/v4';
  const headers = { 'X-Auth-Token': API_KEY };

  try {
    if (type === 'standings') {
      const res = await fetch(`${base}/competitions/${leagueId}/standings`, { headers });
      const data = await res.json();
      return {
        statusCode: 200,
        headers: { 'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      };
    }

    if (type === 'fixtures') {
      const res = await fetch(`${base}/competitions/${leagueId}/matches?status=SCHEDULED`, { headers });
      const data = await res.json();
      return {
        statusCode: 200,
        headers: { 'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      };
    }

    // Fetch both in parallel
    const [sRes, fRes] = await Promise.all([
      fetch(`${base}/competitions/${leagueId}/standings`, { headers }),
      fetch(`${base}/competitions/${leagueId}/matches?status=SCHEDULED`, { headers }),
    ]);

    const [standings, fixtures] = await Promise.all([sRes.json(), fRes.json()]);

    return {
      statusCode: 200,
      headers: { 'Access-Control-Allow-Origin': '*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ standings, fixtures }),
    };

  } catch (err) {
    return {
      statusCode: 500,
      headers: { 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ error: err.message }),
    };
  }
};
