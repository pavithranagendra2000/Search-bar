# Search-bar
Search bar to search countries name
npm start
npx create-react-app country-search
cd country-search
npm install axios react-autocomplete
import axios from 'axios';

const API_URL = 'https://restcountries.com/v3.1/all'; // Replace with your JSON data URL

export const fetchCountries = async () => {
  const response = await axios.get(API_URL);
  return response.data;
};
import React, { useState, useEffect } from 'react';
import { fetchCountries } from './api'; // Import the fetch function
import Autocomplete from 'react-autocomplete';

const SearchBar = () => {
  const [countries, setCountries] = useState([]);
  const [filteredCountries, setFilteredCountries] = useState([]);
  const [searchTerm, setSearchTerm] = useState('');

  useEffect(() => {
    const getCountries = async () => {
      const data = await fetchCountries();
      setCountries(data);
    };

    getCountries();
  }, []);

  useEffect(() => {
    const results = countries.filter(country =>
      country.name.common.toLowerCase().includes(searchTerm.toLowerCase()) ||
      (country.capital && country.capital.some(capital =>
        capital.toLowerCase().includes(searchTerm.toLowerCase())
      ))
    );
    setFilteredCountries(results);
  }, [searchTerm, countries]);

  return (
    <div className="search-bar">
      <Autocomplete
        getItemValue={(item) => item.name.common}
        items={filteredCountries}
        renderItem={(item, isHighlighted) => (
          <div
            key={item.cca3}
            style={{ background: isHighlighted ? '#ddd' : '#fff' }}
          >
            {item.name.common}
          </div>
        )}
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        onSelect={(val) => setSearchTerm(val)}
        inputProps={{ placeholder: 'Search for a country or capital...' }}
      />
    </div>
  );
};

export default SearchBar;
.search-bar {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: 10px;
}

.search-bar input {
  width: 100%;
  padding: 10px;
  font-size: 16px;
}

.search-bar .react-autocomplete-dropdown {
  border: 1px solid #ddd;
  max-height: 200px;
  overflow-y: auto;
}
import './SearchBar.css';
import React from 'react';
import SearchBar from './SearchBar';

function App() {
  return (
    <div className="App">
      <h1>Country Search</h1>
      <SearchBar />
    </div>
  );
}

export default App;

