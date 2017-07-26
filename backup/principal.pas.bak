unit Principal;

{$mode objfpc}{$H+}

interface

uses
  Classes, SysUtils, FileUtil, TAGraph, TASeries, Forms, Controls, Graphics,
  Dialogs, StdCtrls, Spin, Buttons, Grids, ExtCtrls;

type

  { TForm1 }

  TForm1 = class(TForm)
    Chart1: TChart;
    Chart1AreaSeries1: TAreaSeries;
    Chart1LineSeries1: TLineSeries;
    Edit1: TEdit;
    Edit2: TEdit;
    Edit3: TEdit;
    GroupBox1: TGroupBox;
    GroupBox2: TGroupBox;
    GroupBox4: TGroupBox;
    Image1: TImage;
    Label1: TLabel;
    Label2: TLabel;
    Label3: TLabel;
    Label4: TLabel;
    RadioButton1: TRadioButton;
    RadioButton2: TRadioButton;
    RadioButton3: TRadioButton;
    RadioButton4: TRadioButton;
    SpeedButton1: TSpeedButton;
    SpeedButton2: TSpeedButton;
    SpeedButton3: TSpeedButton;
    SpinEdit1: TSpinEdit;
    StringGrid1: TStringGrid;
    procedure FormCreate(Sender: TObject);
    procedure SpeedButton1Click(Sender: TObject);
    procedure SpeedButton2Click(Sender: TObject);
    procedure SpeedButton3Click(Sender: TObject);
  private
    { private declarations }
  public
    { public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.lfm}

{ TForm1 }

uses
  LCLIntf;

var
  n: Integer;
  a, b, h, Integral: Extended;
  f: String;
  p, fp: Extended;
  w, t: array[0..15] of Extended;
  x, y: array[0..1000] of Extended;
  nGauss: Integer;
  Erro: Word;

function FxR1(f: String; x: Extended; var y: Extended): Word;
stdcall;
external 'Interpretador.dll';


//MÉTODOS

function RetangulosE: Extended;
var
  i: Integer;
  Soma: Extended;
begin
  Soma := 0;
  for i := 0 to n-1 do
    begin
      Soma := Soma + y[i];
    end;
  Result := Soma * h;
end;

function RetangulosD: Extended;
var
  i: Integer;
  Soma: Extended;
begin
  Soma := 0;
  for i := 1 to n do
    begin
      Soma := Soma + y[i];
    end;
  Result := Soma * h;
end;

function Trapezios: Extended;
var
  i: Integer;
  Soma: Extended;
begin
  Soma := (y[0] + y[n]) / 2;
  for i := 1 to n-1 do
    begin
      Soma := Soma + y[i];
    end;
  Result := Soma * h;

end;

function Simpson13: Extended;
var
  i: Integer;
  SomaPar, SomaImpar: Extended;
begin
  SomaPar := 0;
  SomaImpar := 0;
  for i := 1 to Trunc(n/2) do
    begin
      SomaImpar := SomaImpar + y[i*2 - 1];
//      if i <> Trunc(n/2) then

    end;
  for i := 1 to Trunc(n/2) - 1 do
    SomaPar := SomaPar + y[i*2];

  Result := h*(y[0] + y[n] + 4*SomaImpar + 2*SomaPar)/3;

end;

function Simpson38: Extended;
var
  i: Integer;
  SomaMultiplo3, SomaNaoMultiplo3: Extended;
begin
  SomaNaoMultiplo3 := 0;
  SomaMultiplo3 := 0;

  for i := 1 to Trunc(n/3) do
    begin
      SomaNaoMultiplo3 := SomaNaoMultiplo3 + y[(3*i) - 2] + y[(3*i) - 1];
  //    if i <> Trunc(n/3) then

    end;

  for i := 1 to Trunc(n/3)-1 do
    SomaMultiplo3 := SomaMultiplo3 + y[3*i];

  Result := 3*h*(y[0] + 3*SomaNaoMultiplo3 + 2*SomaMultiplo3 + y[n]) / 8;

end;

function Gauss: Extended;
var
  i: Integer;
  SomaF, xGauss, yGauss: Extended;
begin
  SomaF := 0;
  for i := 0 to nGauss - 1 do
    begin
      xGauss := (a*(1-t[i]) + b*(1+t[i]))/ 2;
      Erro := FxR1(f, xGauss, yGauss);
      if Erro <> 0 then
        begin
          ShowMessage('Erro ao avaliar a função no método de Gauss.');
          Exit;
        end;

      SomaF := SomaF + w[i] * yGauss;
    end;

  Result := (b - a) * SomaF / 2;

end;


procedure PontosPesosGauss(nGauss: Integer);
(*$I twConst.pas *)
var
  k: Integer;
begin
  FillChar(w, SizeOf(w), 0);
  FillChar(t, SizeOf(t), 0);
  case nGauss of
    2: for k:=0 to 1 do
         begin
           w[k] := wConst2[k];
           t[k] := tConst2[k];
         end;
    4: for k:=0 to 3 do
         begin
           w[k] := wConst4[k];
           t[k] := tConst4[k];
         end;
    8: for k:=0 to 7 do
         begin
           w[k] := wConst8[k];
           t[k] := tConst8[k];
         end;
    16: for k:=0 to 15 do
         begin
           w[k] := wConst16[k];
           t[k] := tConst16[k];
         end;
  end;
end;


procedure TForm1.FormCreate(Sender: TObject);
begin
  RadioButton1.Checked := True;
  SpinEdit1.Value := 50;
  with StringGrid1 do
    begin
      Cells[0,0] := 'Método';
      Cells[1,0] := 'Resultado';
      Cells[0,1] := 'Retângulos à Esquerda';
      Cells[0,2] := 'Retângulos à Direita';
      Cells[0,3] := 'Regra dos Trapézios';
      Cells[0,4] := 'Regra 1/3 de Simpson';
      Cells[0,5] := 'Regra 3/8 de Simpson';
      Cells[0,6] := 'Método de Gauss';
    end;


end;

procedure TForm1.SpeedButton1Click(Sender: TObject);
var
  i: Integer;
begin
  f := Trim(Edit1.Text);
  if f = '' then
    begin
      ShowMessage('Informe a função.');
      Edit1.SetFocus;
      Exit;
    end;

  try
    a := StrToFloat(Edit2.Text);
  except
    ShowMessage('Valor de a incorreto.');
    Edit2.SetFocus;
    Exit;
  end;

  try
    b := StrToFloat(Edit3.Text);
  except
    ShowMessage('Valor de b incorreto.');
    Edit3.SetFocus;
    Exit;
  end;

  if b <= a then
    begin
      ShowMessage('b deve ser maior do que a.');
      Edit3.SetFocus;
      Exit;
    end;

  n := SpinEdit1.Value;
  h := (b-a)/n;

  for i := 0 to n do
    begin
      p := a + i*h;
      Erro := FxR1(f, p, fp);
      if Erro <> 0 then
        begin
          ShowMessage('Erro ao avaliar a função no ponto: ' + FloatToStr(p) + '.');
          Exit;
        end;
      x[i] := p;
      y[i] := fp;
    end;

  if RadioButton1.Checked then
    nGauss := 2;
  if RadioButton2.Checked then
    nGauss := 4;
  if RadioButton3.Checked then
    nGauss := 8;
  if RadioButton4.Checked then
    nGauss := 16;

  PontosPesosGauss(nGauss);

  try
    StringGrid1.Cells[1,1] := FloatToStr(RetangulosE);
  except
    StringGrid1.Cells[1,1] := 'Erro ao calcular a integral';
  end;
  try
    StringGrid1.Cells[1,2] := FloatToStr(RetangulosD);
  except
    StringGrid1.Cells[1,2] := 'Erro ao calcular a integral';
  end;
  try
    StringGrid1.Cells[1,3] := FloatToStr(Trapezios);
  except
    StringGrid1.Cells[1,3] := 'Erro ao calcular a integral';
  end;
  try
    StringGrid1.Cells[1,4] := FloatToStr(Simpson13);
  except
    StringGrid1.Cells[1,4] := 'Erro ao calcular a integral';
  end;
  try
    StringGrid1.Cells[1,5] := FloatToStr(Simpson38);
  except
    StringGrid1.Cells[1,5] := 'Erro ao calcular a integral';
  end;
  try
    StringGrid1.Cells[1,6] := FloatToStr(Gauss);
  except
    StringGrid1.Cells[1,6] := 'Erro ao calcular a integral';
  end;

  Chart1LineSeries1.Clear;
  Chart1AreaSeries1.Clear;
  for i := 0 to n do
    begin
      Chart1LineSeries1.AddXY(x[i], y[i]);
      Chart1AreaSeries1.AddXY(x[i], y[i]);
    end;

end;

procedure TForm1.SpeedButton2Click(Sender: TObject);
begin
  Edit1.Text := '';
  Edit2.Text := '';
  Edit3.Text := '';
  RadioButton1.Checked := True;
  StringGrid1.Clean([gzNormal]);
  Chart1LineSeries1.Clear;
  Chart1AreaSeries1.Clear;
  SpinEdit1.Value := 50;
  n := 50;
end;

procedure TForm1.SpeedButton3Click(Sender: TObject);
begin
  if not OpenDocument('Integrais ajuda.pdf') then
    MessageDlg('Informação', 'Arquivo de ajuda não foi encontrado', mtInformation, [mbOk], 0);
end;

end.

