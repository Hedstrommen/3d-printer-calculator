# 3D Print Price Calculator

A web app for calculating what a 3D print costs to make — and what you should sell it for.

Runs **entirely in the browser** (C# via Blazor WebAssembly): no server, no hosting cost, and it **works offline** after your first visit.

**Try it yourself:** `https://hedstrommen.github.io/3D-Printer-calculator/`

# How it looks like:
<img width="974" height="1212" alt="image" src="https://github.com/user-attachments/assets/47764434-c5b4-4baa-a918-08c03dcaf2f7" />

## Features

- **Filament cost** — enter spool price, spool weight, and model weight; get the material cost
- **Sales multiplier** — e.g. `3.0` means a 100 kr print sells for 300 kr
- **Optional fixed fee** — machine wear, nozzles, etc.
- **Optional printer-hour cost** — electricity and wear based on print duration
- **Currency switch** — SEK (default), EUR, USD
- **Remembers your inputs** — saved in your browser between visits
- **Works offline** — after the first visit it runs without internet (PWA service worker)
- **Phone-friendly** — installable as an app from the browser, responsive layout
- **Green & teal theme** with soft animations

## Project layout

```
src/
  PrintCostCalculator.Core/          # All calculation logic, no UI
    Models/
      PrintCostInput.cs              # What the user types in
      PrintCostResult.cs             # The price breakdown
    Services/
      IPrintCostCalculator.cs        # The calculation contract
      PrintCostCalculatorService.cs   # The default implementation
      PrintPriceFormatter.cs         # Currency formatting (SEK/EUR/USD)

  PrintCostCalculator.Web/           # The web UI (Blazor WebAssembly, static)
    Components/Pages/CalculatorPage.razor   # The whole UI
    Services/
      IUserInputStore.cs            # Contract for remembering inputs
      BrowserUserInputStore.cs      # Browser local-storage implementation
    wwwroot/                        # Static files (index.html, CSS, PWA bits)
```

## How the price is calculated

```
pricePerGram = filamentPurchasePrice / filamentSpoolWeightInGrams
materialCost = pricePerGram * modelWeightInGrams

totalCost    = materialCost + extraFixedFeePerPrint + (costPerPrinterHour * printDurationInHours)
sellingPrice = totalCost * salesMultiplier
```

## Modding it

Everything is wired through dependency injection in `src/PrintCostCalculator.Web/Program.cs`:

```csharp
builder.Services.AddScoped<IPrintCostCalculator, PrintCostCalculatorService>();
```

Want a different pricing model? Write a new `IPrintCostCalculator` implementation and swap one line. Same goes for the input store (save somewhere else instead of the browser) and the currency formatter.
