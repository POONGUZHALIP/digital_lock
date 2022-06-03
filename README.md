# digital_lock
verilog
module charge(clk,rst,a,b,c,d,lock,try_cnt,alaram,enter);
  input clk,rst;
  input [2:0] a,b,c,d;//eacach password it will support 0 to 7
  input enter;
  output reg lock;
  output reg alaram;
  output reg  [2:0] try_cnt;
  parameter s0=3'b000;
  parameter s1=3'b001;
  parameter s2=3'b010;
  parameter s3=3'b011;
  parameter s4=3'b100;
  parameter s5=3'b101;
  parameter s6=3'b110;
  reg [2:0] n_state, p_state;
  always @(posedge clk)begin
    if(rst==1)
    begin
      lock=1;
      alaram=0;
      try_cnt=0;
      n_state=s0;
      p_state=s0;
    end
     else
       begin
      p_state = n_state;
      case(p_state)
        s0:begin
          if(a== 1)begin
            n_state=s1;
          end
          if(a!=1)begin
            try_cnt=try_cnt+1;
            n_state=s4;
          end
        end
        s1:begin
          if(b==2)begin
            n_state=s2;
          end
          if(b!=2)begin
            try_cnt=try_cnt+1;
            n_state=s4;
          end
        end
        s2:begin
          if(c==3)begin
            n_state=s3;
          end
          if(c!=3)begin
           try_cnt=try_cnt+1;
            n_state=s4;
          end
        end
        s3:begin
          if(d==4)begin
             n_state=s6;
          end
          if(d!=4)begin
           try_cnt=try_cnt+1;
            n_state=s4;
          end
        end
        s4:begin
          if(try_cnt<3)
            begin
              n_state=s0;
            end
          if(try_cnt==3)
            begin
              n_state=s5;
            end
        end
        s5:begin
          alaram=1;
          repeat(2)@(posedge clk);
          alaram=0;
        end
      	s6:begin
          if(enter==1)begin
            lock=0;
          end
        end
      endcase
    end
  end
endmodule 
module top;
  reg clk,rst;
  reg [2:0] a,b,c,d;//eacach password it will support 0 to 7
  reg enter;
  wire lock;
  wire alaram;
  wire [2:0] try_cnt;
 //  wire [2:0] n_state, p_state;
  charge dut(clk,rst,a,b,c,d,lock,try_cnt,alaram,enter);
  initial begin
    clk=0;
    forever #5 clk=~clk;
  end
  initial begin
    rst=1;
    #15;
    rst=0;
  end
  initial begin
    #20;
    a=1;
    #10;
   b=2;
    #10;
    c=3;
    #10
    d=4;
    enter=1;
    #40;//
    $display("a=%d b=%d c=%d d=%d lock=%d alaram=%d try_cnt=%d",a,b,c,d,lock,alaram,try_cnt);
    #10;
     $finish;
  end

 initial begin
    $dumpfile("dump.vcd");
    $dumpvars;
  end
endmodule
