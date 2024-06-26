# Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink
The project features the implementation of a data-driven Model Predictive Controller (MPC), offering ease of utilization. MPC, commonly applied in real-world and industrial settings, is favored for its straightforwardness, prompting researchers to integrate it into their applications. This project specifically adopts the data-driven approach, wherein the controller operates without prior knowledge of the target system. This characteristic renders it particularly appealing, as it can be effectively utilized without specific insights into the system. Integration into your Simulink setup is seamless, requiring only the connection of the system's output and desired signals.
<h2>Introduction</h2>
This article demonstrates the implementation of a data-driven model predictive controller (MPC) within the Simulink environment using MATLAB. The controller leverages real-time data provided by the original system alongside desired reference signals. Essentially, the controller endeavors to align the system's output with the desired signal by evaluating the control input. This control input, generated by the controller, is subsequently applied to the original system. Consequently, the controller features two input ports for the system's output and desired reference signal, and one output port for the control input.

The controller is encapsulated within a Simulink subsystem, facilitating easy parameter modification. Users can adjust the subsystem's parameters by double-clicking on it and inserting the desired controller parameters, which are outlined below.

The developed subsystem employs an Autoregressive Integrated Moving Average (ARIMA) model to fit the real system's outputs and inputs. Subsequently, an MPC is applied to the online estimated ARIMA model to achieve the control objective, which primarily aims to align the system's outputs with the desired reference signals. The subsequent section showcases the developed subsystem.
<br>
<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/73f830ba-1d05-480e-8464-d2284afa0aa7">
<br>
The developed subsystem as shown in the above picture can be directly used by connecting its ports into the original system that is desired to be controlled.

<h2>Context</h2>

As previously outlined, the Simulink subsystem developed in this project incorporates both online model fitting and model predictive control (MPC). Initially, the focus is on establishing an ARIMA model, proceeding as follows:

    y(t)=A1*y(t-1)+A2*y(t-2)+...+Ap*y(t-p)+B1*u(t-1)+B2*u(t-2)+...+Bq*u(t-q)

In the subsequent phase, the developed subsystem fits a model similar to the aforementioned equation onto both the inputs and outputs of the original system, where y(t) and u(t) denote the system's outputs and inputs, respectively. Parameters mu and my signify the numbers of outputs and inputs of the original system, while p and q represent the orders of the ARIMA model utilized in the equation. These parameters are adjustable manually by modifying them within the subsystem's parameters. Notably, this model fitting occurs within the context of an original system with fully unknown internal properties.

Moving forward, the next step involves the application of an appropriate MPC strategy to the established ARIMA model. The objective of the controller is to guide the system's outputs, denoted as y(t), towards the desired reference signals, represented by yd(t), by devising a suitable control sequence grounded in MPC principles. Within the developed subsystem, parameters Ny and Nu designate the prediction and control horizons, respectively. Essentially, the controller anticipates the future trajectory of the system's output y(t) in upcoming steps, as delineated below:

    y(t+1)=A1*y(t)+A2*y(t-1)+...+Ap*y(t-p+1)+B1*U(t)+B2*u(t-1)+...+Bq*u(t-q+1)

    y(t+2)=A1*y(t+1)+A2*y(t)+...+Ap*y(t-p+2)+B1*U(t+1)+B2*U(t)+B3*u(t-1)...+Bq*u(t-q+2)

    y(t+Ny)=A1*y(t+Ny-1)+A2*y(t+Ny-2)+...+Ap*y(t+Ny-p)+B1*U(t+Ny-1)+B2*U(Ny-2)+...

Subsequently, the controller meticulously calculates the control sequence U=[U(t) U(t+1) ... U(t+Nu-1)] such that the predicted outputs Y=[y(t+1) y(t+2) ... y(t+Ny)] closely approximate the reference signals yd(t). To achieve this objective, an optimization problem is formulated and solved to determine the control sequence U optimally. At each step t, the optimal control sequence is obtained, and its first entry is applied to the original system as the control input.

It's important to note that the developed subsystem is applicable to both Single Input Single Output (SISO) and Multiple Input Multiple Output (MIMO) systems, allowing for systems with more than one output and input ports.

<h2>Utilizing the Code</h2>
In this section, explanatory notes regarding the utilization of the developed subsystem are provided through illustrative examples.

In the first example, let's consider the scenario where the aim is to control the Linear Time-Invariant (LTI) system 1/(s+1) to track a sine-wave reference signal, depicted in the following illustration:

<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/37bcbe1e-80c8-441a-9c28-2334618eb76a">

The described image illustrates the straightforward utilization of the developed controller within Simulink. To implement this, the original system (1/(s+1)) is instantiated, with its output linked to the "y" port of the Data-Driven MPC, and its input connected to the "uc" port of the same MPC block. Additionally, a sine-wave is designated as the desired reference signal, with adjustable parameters. Subsequently, the controller endeavors to converge the output signal of the original system towards the desired signal.

Upon clicking on the Data-Driven MPC block, the following properties are displayed:

<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/a4678f0c-0450-41de-82a1-01d8a3daf3bf">

Some parameters have been defined previously, while others are introduced here. Parameter "T" signifies the time step. Parameters "umin", "umax", and "udmax" denote the minimum, maximum, and maximum time-derivative values of the control input signal, respectively. These parameters are subject to adjustment to accommodate the limitations of the original system.

Subsequently, the performance of the controller is evaluated by displaying the system's output alongside its desired signal. This comparison provides insight into the effectiveness of the controller.

<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/cb30ac73-dfdb-4a10-85be-bd2a762d0da3">

ChatGPT

The controller demonstrates effective control over the system, as evidenced by the results.

Moving on to the second example, the data-driven MPC is deployed to regulate the water level in a water-tank system. Further details regarding this example can be found in the Example2.slx file provided in the attached document. Below is a representation of the system:

<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/eee21c13-85a6-4502-89b9-f33a6792e2fc">

In this example, the desired water level is designated as 2, and the Data-Driven MPC is employed to achieve this control objective. Remarkably, the controller can be seamlessly utilized without any prior knowledge of the internal workings of the original system, which, in this case, is a water-tank system. Operators can adjust the controller's parameters according to their limits and commands. The figure below illustrates the successful tracking of the system's output signal:

<img src="https://github.com/BenamaraMustapha/Implementing-a-Data-Driven-Model-Predictive-Controller-MPC-in-MATLAB-Simulink/assets/119163433/f0089cf4-622d-4d17-b466-e6bb7d66d0e9">

The depicted figure demonstrates the capability of the implemented controller to effectively regulate the water level in this example.

The developed subsystem incorporates a script, denoted by "myfun," which facilitates both online estimation and MPC design. The code for this script is provided below:

	function Z=myfun(y,yref,u,t,umin,umax,udmax,p,q,my,mu,Ny,Nu,T,th0,P0)
 	global U Y P th Uo yh;
	U=[U(2:end,:);u'];
 	if t==0      
  	    P=P0;th=th0;
    	  U=zeros(q,mu);Y=zeros(p,my);
    	  Uo=zeros(Nu,mu);    
    end
    H=zeros(my,my^2*p+my*mu*q);
    ind=1;
    for i=1:p
        H(:,ind:ind+my^2-1)=kron(Y(p-i+1,:),eye(my));ind=ind+my^2;
    end
    for i=1:q
      H(:,ind:ind+my*mu-1)=kron(U(q-i+1,:),eye(my));ind=ind+my*mu;
    end
    H=H';
    m=size(H,2);
    L=(P*H)/(eye(m)+H'*P*H);
    P=P-L*H'*P;
    th=th+L*(y'-H'*th);
    yh=zeros(my,1);
    for i=1:p
      ind=1+(i-1)*my^2;
      Ai=reshape(th(ind:ind+my*my-1),my,my);
      yh=yh+Ai*Y(p-i+1,:)';
    end
    for i=1:q
      ind=1+p*my^2+(i-1)*my*mu;
      Bi=reshape(th(ind:ind+my*mu-1),my,mu);
      yh=yh+Bi*U(q-i+1,:)';
    end
    Y=[Y(2:end,:);y'];
    umin=reshape(umin,length(umin),1);
    umax=reshape(umax,length(umax),1);
    udmax=reshape(udmax,length(udmax),1);
    G=zeros(Ny*my,Nu*mu);g=zeros(Ny*my,1);
    for i=1:Ny
      Ge=zeros(my,size(G,2));ge=zeros(my,1);
      for j=1:p
          ind=1+(j-1)*my^2;
          Aj=reshape(th(ind:ind+my*my-1),my,my);
          if j>=i
              ge=ge+Aj*Y(end-j+i,:)';
          else
              Gw=G((i-j-1)*my+1:(i-j)*my,:);
              gw=g((i-j-1)*my+1:(i-j)*my,:);
              Ge=Ge+Aj*Gw;
              ge=ge+Aj*gw;
          end
      end
      for j=1:q
          ind=1+p*my^2+(j-1)*my*mu;
          Bj=reshape(th(ind:ind+my*mu-1),my,mu);
          if j>i
              ge=ge+Bj*U(end-j+i+1,:)';
          elseif i-j+1<=Nu
              Ge(:,(i-j)*mu+1:(i-j+1)*mu)=Ge(:,(i-j)*mu+1:(i-j+1)*mu)+Bj;
          end
      end
      G((i-1)*my+1:i*my,:)=Ge;
      g((i-1)*my+1:i*my,:)=ge;
    end
    z1=ones(Nu,1);
    if ~isempty(udmax)
      A1=eye(Nu*mu);b1=Uo+T*kron(z1,udmax);
      A2=-eye(Nu*mu);b2=-Uo+T*kron(z1,udmax);
      A=[A1;A2];b=[b1;b2];
      else
      A=[];b=[];
    end
    if ~isempty(umin)
      Umin=kron(z1,umin);
      else
       Umin=[];
    end
    if ~isempty(umax)
      Umax=kron(z1,umax);
    else
      Umax=[];
    end
    Yref=kron(ones(Ny,1),yref);
    gb=g-Yref;
    opt=optimset('disp','none');
    Uo=quadprog(2*(G'*G),2*G'*gb,A,b,[],[],Umin,Umax,Uo,opt);
    uc=Uo(1:mu);
    Z=[uc;yh];
    end


The provided code is integral to understanding data-driven Model Predictive Control (MPC) within the developed Simulink subsystem. Here are the key features:

- Integration Flexibility: Seamlessly integrates into existing control frameworks, facilitating its use in various control loops.

- Benchmarking Potential: Serves as a benchmark in scientific articles for comparative analysis of controller performance.

- Ease of Use: User-friendly interface and plug-and-play functionality make it accessible to users of all levels.

- Comprehensive Evaluation: Enables thorough performance assessments, including efficacy, stability, and robustness.

- Educational Resource: Offers practical learning experience for MPC concepts and application.

- Adaptability: Customizable to suit specific control requirements and system dynamics.

- Contribution to Knowledge Sharing: Embraces open-source principles, fostering collaboration and innovation within the control systems community.
  git status
