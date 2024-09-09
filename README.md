
# Async API Fetcher

This project demonstrates a common issue in JavaScript related to handling asynchronous operations and race conditions. It fetches data from a public API but contains a subtle bug caused by incorrect usage of `async/await` and `forEach`, which results in an empty or partially filled data array. The project is useful for understanding and fixing race conditions in Node.js.

## Table of Contents

- [Introduction](#introduction)
- [Setup](#setup)
- [Usage](#usage)
- [Problem Overview](#problem-overview)
- [Solution](#solution)
- [License](#license)

## Introduction

In this project, we attempt to fetch data from multiple public APIs asynchronously and process the data. However, due to improper handling of asynchronous loops, the program doesn’t work as expected, and the issue can be difficult to debug. The project is designed to be simple but highlights an important pitfall in JavaScript's asynchronous programming model.

## Setup

Follow these steps to set up and run the project locally:

1. Clone or download the repository:
   ```bash
   git clone https://github.com/your-username/async-api-fetcher.git
   cd async-api-fetcher
   ```

2. Initialize the project and install dependencies:
   ```bash
   npm install
   ```

   This will install the only required dependency, `axios`, which is used to make HTTP requests.

3. You are now ready to run the project.

## Usage

1. To run the project, simply execute the following command in your terminal:
   ```bash
   node index.js
   ```

2. The program will attempt to fetch data from several public APIs, simulate some processing time for each API call, and then log the processed data.

   However, due to an issue in the asynchronous handling of API calls, the data array will often be empty or incomplete.

## Problem Overview

The issue with the current implementation lies in how the asynchronous operations are handled. The code uses `forEach` with `async/await`, but `forEach` does not work well with promises because it doesn't wait for each iteration to complete before proceeding.

### Problem Code (in `index.js`)

```javascript
async function processApiData() {
  const dataResults = [];

  apiUrls.forEach(async (url) => {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    const data = await fetchDataFromApi(url);
    dataResults.push(data);
  });

  console.log('Processed data:', dataResults);
}
```

In this code, `dataResults.push(data)` is executed before the promises resolve, resulting in an incomplete or empty array when the data is logged.

### Expected Output

The expected output should be an array of data fetched from the APIs, but instead, the output will typically be an empty array.

## Solution

To fix the issue, we can replace the `forEach` loop with `Promise.all` and properly handle the asynchronous calls.

### Fixed Code

```javascript
async function processApiData() {
  const promises = apiUrls.map(async (url) => {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    return fetchDataFromApi(url);
  });

  const dataResults = await Promise.all(promises);
  console.log('Processed data:', dataResults);
}
```

### Running the Fixed Code

Once you replace the problematic code with the solution above, running the program again will produce the correct output, with all API data properly fetched and logged.

## License

This project is open-source and available under the [MIT License](LICENSE).
