# MMA21x55_V1
# Moving Average Cross Strategy (NTSL / Neológica)

This repository contains a simple moving average cross strategy written in **NTSL** for Neológica's Profit platform. The strategy triggers buy/sell signals whenever a short moving average (MA) crosses above or below a longer moving average, and sets partial take-profit and stop-loss orders.


## Features

- **Short Moving Average (21)** and **Long Moving Average (55)**  
- **Partial Take-Profit** with `PontosStopGainParcial`  
- **Stop-Loss** to limit risk with `PontosStopLoss`  
- Automatically closes previous positions when reversing signal

## How it Works

1. **Buy signal**: Occurs when the short MA crosses above the long MA.  
   - Closes any short positions.  
   - Opens a new long position (buy).  
   - Places stop-loss and partial take-profit orders.

2. **Sell signal**: Occurs when the short MA crosses below the long MA.  
   - Closes any long positions.  
   - Opens a new short position.  
   - Places stop-loss and partial take-profit orders.

## Parameters

- `MediaRapida (21)`: Period for the short MA  
- `MediaLenta (55)`: Period for the long MA  
- `PontosStopGainParcial (1000)`: Points for the partial take-profit  
- `PontosStopLoss (500)`: Points for the stop-loss  
- `QuantidadeStopParcialGain (5)`: Quantity of partial execution  

Feel free to adjust these parameters for your preferred settings.

## Usage

1. **Open Profit/Neológica** and go to the strategy editor (`NTSL` environment).  
2. **Copy** the content of [`src/estrategia-nts.txt`](src/estrategia-nts.txt) into a new strategy script.  
3. **Compile/Validate** the script.  
4. **Apply** it on your chart to see the signals and orders being generated.

### Example Code (NTSL)

```pascal
Parâmetro
  MediaRapida(21);
  MediaLenta(55);
  PontosStopGainParcial(1000);
  PontosStopLoss(500);
  QuantidadeStopParcialGain(5);

var                      
  sMedRapida      : Real;
  sMedLenta       : Real;
  sPrevMedRapida  : Real;
  sPrevMedLenta   : Real;

begin
  sMedRapida      := Media(MediaRapida, Fechamento);
  sMedLenta       := Media(MediaLenta, Fechamento);
  sPrevMedRapida  := sMedRapida[1];
  sPrevMedLenta   := sMedLenta[1];

  // Buy signal (short MA crosses above long MA)
  se (sPrevMedRapida < sPrevMedLenta) e (sMedRapida > sMedLenta) então
    inicio
      Se (isSold = true) então
        BuyToCoverLimit(sMedLenta);

      BuyLimit(sMedLenta);
      SellToCoverStop(sMedLenta - PontosStopLoss);
      SellToCoverLimit(sMedLenta + PontosStopGainParcial, QuantidadeStopParcialGain);
    fim

  // Sell signal (short MA crosses below long MA)
  senão se (sPrevMedRapida > sPrevMedLenta) e (sMedRapida < sMedLenta) então 
    inicio
      Se (IsBought = true) então
        SellToCoverLimit(sMedLenta);

      SellShortLimit(sMedLenta);
      BuyToCoverStop(sMedLenta + PontosStopLoss);
      BuyToCoverLimit(sMedLenta - PontosStopGainParcial, QuantidadeStopParcialGain);
    fim;

  // Plotting
  Plot(sMedRapida);
  Plot2(sMedLenta);

  se (sMedRapida > sMedLenta) então
    paintBar(clGreen);

  se (sMedRapida < sMedLenta) então
    paintBar(clRed);
End;
