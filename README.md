// frontend/src/App.jsx
import React, { useState } from "react";

export default function App() {
  const [weights, setWeights] = useState({
    safety_weight: 0.4,
    commute_weight: 0.3,
    amenities_weight: 0.2,
    schools_weight: 0.1,
  });

  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  const handleChange = (e) => {
    setWeights({
      ...weights,
      [e.target.name]: Number(e.target.value) || 0,
    });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    const totalWeight = Object.values(weights).reduce((a, b) => a + b, 0);
    if (Math.abs(totalWeight - 1) > 0.001) {
      alert("Weights must add up to 1.0");
      return;
    }

    setLoading(true);
    try {
      const response = await fetch("http://localhost:5000/match", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(weights),
      });

      if (!response.ok) {
        alert("Error fetching results");
        return;
      }

      const data = await response.json();
      setResults(data);
    } catch (error) {
      console.error(error);
      alert("An error occurred");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      <h1>NeighborFit</h1>
      <form onSubmit={handleSubmit}>
        <label htmlFor="safety_weight">
          Safety Weight:{" "}
          <input
            id="safety_weight"
            type="number"
            step="0.1"
            min="0"
            max="1"
            name="safety_weight"
            value={weights.safety_weight}
            onChange={handleChange}
          />
        </label>
        <br />
        <label htmlFor="commute_weight">
          Commute Weight:{" "}
          <input
            id="commute_weight"
            type="number"
            step="0.1"
            min="0"
            max="1"
            name="commute_weight"
            value={weights.commute_weight}
            onChange={handleChange}
          />
        </label>
        <br />
        <label htmlFor="amenities_weight">
          Amenities Weight:{" "}
          <input
            id="amenities_weight"
            type="number"
            step="0.1"
            min="0"
            max="1"
            name="amenities_weight"
            value={weights.amenities_weight}
            onChange={handleChange}
          />
        </label>
        <br />
        <label htmlFor="schools_weight">
          Schools Weight:{" "}
          <input
            id="schools_weight"
            type="number"
            step="0.1"
            min="0"
            max="1"
            name="schools_weight"
            value={weights.schools_weight}
            onChange={handleChange}
          />
        </label>
        <br />
        <button type="submit">Find Match</button>
      </form>

      {loading && <p>Loading...</p>}

      <h2>Results:</h2>
      {results.length > 0 ? (
        <ul>
          {results.map((n) => (
            <li key={n.name}>
              {n.name}: {n.score}
            </li>
          ))}
        </ul>
      ) : (
        !loading && <p>No results yet.</p>
      )}
    </div>
  );
}
