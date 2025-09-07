<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>README.md</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji";
            line-height: 1.6;
            color: #24292e;
            background-color: #ffffff;
            margin: 0 auto;
            padding: 45px;
            max-width: 960px;
        }
        h1, h2, h3, h4, h5, h6 {
            color: #000000;
            margin-top: 24px;
            margin-bottom: 16px;
            font-weight: 600;
            line-height: 1.25;
            border-bottom: 1px solid #eaecef;
            padding-bottom: 0.3em;
        }
        h1 { font-size: 2em; }
        h2 { font-size: 1.5em; }
        h3 { font-size: 1.25em; }
        p { margin-bottom: 16px; }
        code {
            background-color: #f6f8fa;
            border-radius: 6px;
            padding: 0.2em 0.4em;
            font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, Courier, monospace;
            color: #333;
        }
        pre {
            background-color: #f6f8fa;
            border-radius: 6px;
            padding: 16px;
            overflow: auto;
            margin-bottom: 16px;
        }
        pre code {
            background-color: transparent;
            padding: 0;
            color: #333;
        }
        blockquote {
            padding: 0 1em;
            color: #6a737d;
            border-left: 0.25em solid #dfe2e5;
            margin: 0;
            margin-bottom: 16px;
        }
        a {
            color: #0366d6;
            text-decoration: none;
        }
        a:hover {
            text-decoration: underline;
        }
        .directory-tree {
            background-color: #f6f8fa;
            border-radius: 6px;
            padding: 16px;
            font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, Courier, monospace;
            white-space: pre;
            margin-bottom: 16px;
        }
        img {
            max-width: 100%;
            height: auto;
            display: block;
            margin: 16px 0;
            border: 1px solid #eaecef;
            border-radius: 6px;
        }
    </style>
</head>
<body>

<h1>Quantum Teleportation with Qiskit</h1>

<p>This repository contains a Jupyter Notebook that demonstrates the Quantum Teleportation Protocol using IBM's open-source quantum computing framework, Qiskit. It provides a detailed, step-by-step breakdown of how the protocol works, from creating an entangled pair to recovering the teleported state. The notebook uses both statevector and QASM simulators to illustrate the process and verify its success.</p>

<p>Quantum teleportation is a process that transfers the state of a quantum particle from one location to another, without physically moving the particle itself. It is a cornerstone of quantum information science and a fundamental concept for quantum communication.</p>

<h2>Directory Structure</h2>
<pre>
Quantum Teleportation.ipynb
temp.txt
README.md
requirements.txt
</pre>

<h2>Notebook Explanation</h2>

<h3>1. Installing Necessary Libraries</h3>
<p>The notebook begins with a magic command to install the required Qiskit libraries. [cite_start]The <code>%pip install</code> command is a special instruction used in Jupyter notebooks to ensure that the packages are installed directly into the notebook's Python environment, making them immediately available for use[cite: 1, 2, 3, 4].</p>
<pre><code>%pip install qiskit qiskit-aer qiskit-ibm-provider</code></pre>
<p>The packages installed are:</p>
<ul>
<li><strong><code>qiskit</code></strong>: This is the core Quantum SDK (Software Development Kit). [cite_start]It provides the fundamental tools for writing quantum circuits, simulating them, and running them on real quantum computers[cite: 5, 6].</li>
<li><strong><code>qiskit-aer</code></strong>: A high-performance simulator that allows you to run quantum circuits on your local computer's resources. [cite_start]This is essential for testing and debugging circuits quickly without needing access to actual quantum hardware[cite: 7, 8].</li>
<li><strong><code>qiskit-ibm-provider</code></strong>: This package is the bridge between Qiskit and IBM Quantum cloud services. [cite_start]It enables you to connect to your IBM Quantum account and execute your quantum circuits on real IBM quantum computers or their managed simulators[cite: 9, 10].</li>
</ul>

<h3>2. Importing Libraries</h3>
<p>After installation, the notebook imports several modules and classes to build the quantum teleportation circuit. [cite_start]Each import serves a specific purpose in the protocol[cite: 13].</p>
<pre><code>import numpy as np
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister, transpile
from qiskit_aer import AerSimulator
from qiskit.visualization import plot_histogram, plot_bloch_multivector, array_to_latex
from qiskit.circuit.library import Initialize, XGate, ZGate
from qiskit.quantum_info import random_statevector</code></pre>
<p>Key imports explained:</p>
<ul>
[cite_start]<li><code>numpy as np</code>: The standard library for numerical computations, used for manipulating quantum states and probabilities[cite: 14].</li>
[cite_start]<li><code>QuantumCircuit</code>: The primary object for building a quantum program, representing a series of quantum gates and qubits[cite: 15].</li>
[cite_start]<li><code>QuantumRegister</code> & <code>ClassicalRegister</code>: Containers for grouping qubits and classical bits, respectively[cite: 17, 18].</li>
[cite_start]<li><code>transpile</code>: A function that optimizes and restructures a quantum circuit to be compatible with a specific target hardware or simulator[cite: 19].</li>
[cite_start]<li><code>AerSimulator</code>: The simulator from the <code>qiskit-aer</code> package, used to run the circuit locally[cite: 20].</li>
[cite_start]<li><code>plot_bloch_multivector</code>: A visualization tool to represent the state of a qubit on the Bloch sphere[cite: 22].</li>
[cite_start]<li><code>random_statevector</code>: A utility to generate a random, normalized quantum state, which acts as the unknown state to be teleported[cite: 26].</li>
[cite_start]<li><code>Initialize</code>: A special instruction used to prepare a qubit into a specific state vector[cite: 24].</li>
[cite_start]<li><code>XGate</code> and <code>ZGate</code>: The Pauli-X (quantum NOT) and Pauli-Z (phase flip) gates, which are used to apply corrections to the teleported qubit[cite: 25].</li>
</ul>

<h3>3. Helper Functions</h3>
<p>The notebook defines a set of helper functions that implement the core steps of the quantum teleportation protocol. [cite_start]This modular approach makes the main circuit easier to read and understand[cite: 30, 31].</p>
<ul>
<li><strong><code>create_bell_pair(qc, a, b)</code></strong>: This function creates a maximally entangled Bell pair between two qubits, <code>a</code> and <code>b</code>. [cite_start]It first applies a Hadamard (H) gate to qubit <code>a</code> to put it in a superposition, then uses a controlled-NOT (CX) gate with <code>a</code> as the control and <code>b</code> as the target to entangle them[cite: 32, 33, 34, 35].</li>
<li><strong><code>alice_gates(qc, psi, a)</code></strong>: This function represents Alice's part of the protocol. It applies a CNOT gate with the message qubit <code>psi</code> as the control and her half of the Bell pair, <code>a</code>, as the target. It then applies a Hadamard gate to <code>psi</code>. [cite_start]These gates entangle the message qubit with the Bell pair, spreading the quantum information across all three qubits in the system[cite: 36, 37, 38].</li>
<li><strong><code>measure_and_send(qc, a, b)</code></strong>: Alice's measurement step. This function places a barrier and then measures her two qubits, <code>a</code> and <code>b</code>, storing the classical outcomes in two different classical registers (<code>crz</code> and <code>crx</code>). [cite_start]These classical bits are the only information sent to Bob[cite: 39, 40].</li>
<li><strong><code>bob_gates(qc, qubit, crz, crx)</code></strong>: Bob's correction step. Based on the classical bits sent by Alice (<code>crz</code> and <code>crx</code>), Bob applies conditional gates to his entangled qubit. [cite_start]It applies an X gate if <code>crx</code> is 1 and a Z gate if <code>crz</code> is 1. These corrections ensure Bob's qubit is an exact replica of Alice's original state[cite: 41, 42, 43].</li>
[cite_start]<li><strong><code>new_bob_gates(qc, a, b, c)</code></strong>: This is an alternative, more compact implementation of Bob's corrections that uses controlled gates (CNOT and CZ) instead of conditional classical logic, which is also a valid method for performing the same corrections[cite: 44, 45, 46].</li>
</ul>

<h3>4. Circuit Construction and Simulation</h3>

<h4>4.1. Generating and Initializing the Random State</h4>
<p>A random one-qubit quantum state, <code>psi</code>, is generated. [cite_start]This state acts as the unknown quantum information that Alice wants to teleport[cite: 51, 52]. [cite_start]The notebook then uses <code>array_to_latex</code> to display its mathematical representation and <code>plot_bloch_multivector</code> to visualize its position on a Bloch sphere[cite: 54, 56].</p>
[cite_start]<p>The state is then appended to the quantum circuit on qubit <code>qr[0]</code>, which represents Alice's message qubit[cite: 67, 68]. [cite_start]A barrier is used to visually separate this initialization step from the rest of the protocol[cite: 70, 71].</p>
<p></p>

<h4>4.2. Creating the Bell Pair</h4>
<p>The <code>create_bell_pair</code> function is called to entangle Alice's second qubit (<code>qr[1]</code>) and Bob's qubit (<code>qr[2]</code>). [cite_start]This pair, in a maximally entangled state, serves as the quantum channel for teleportation[cite: 77, 78, 79, 84]. A barrier is added after this step to mark the completion of the entanglement setup.</p>
<p></p>

<h4>4.3. Alice's Entanglement and Measurement</h4>
[cite_start]<p>Alice applies her gates using the <code>alice_gates</code> function, which entangles her message qubit (<code>qr[0]</code>) with her half of the Bell pair (<code>qr[1]</code>)[cite: 86, 89]. [cite_start]A measurement is then performed on these two qubits, and the classical results are stored and conceptually "sent" to Bob[cite: 100, 105].</p>
<p></p>

<h4>4.4. Bob's Corrections and Verification</h4>
<p>Bob receives Alice's classical measurement outcomes. Based on these bits, the <code>bob_gates</code> function applies an X gate or a Z gate to his qubit (<code>qr[2]</code>) to perform the necessary corrections. [cite_start]After these corrections, his qubit should now be in the exact state as Alice's original message qubit[cite: 112, 115, 117].</p>
<p>To verify the success of the teleportation, the circuit prepares a final measurement step. [cite_start]The inverse of the original initialization gate is applied to Bob's qubit[cite: 142]. If teleportation was successful, this operation should revert the qubit back to its ground state, $|0\rangle$. [cite_start]The qubit is then measured to confirm that the outcome is indeed 0[cite: 156, 158, 161].</p>
<p></p>

<h3>5. Simulation Results</h3>
<p>The notebook runs the complete circuit on a QASM simulator to get realistic measurement outcomes. The simulation is run for 1024 shots to collect statistical data. [cite_start]The results are presented in a histogram and a dictionary[cite: 164, 171, 174].</p>
<pre><code>{'0 0 1': 245, '0 0 0': 271, '0 1 0': 261, '0 1 1': 247}</code></pre>
<p>The output dictionary shows that Bob's final measurement (represented by the first bit in the string) is always 0. The other two bits correspond to Alice's measurement outcomes. [cite_start]The fact that the first bit is consistently 0 across all shots confirms that the teleportation was successful, as Bob's qubit was correctly restored to its ground state after the inverse initialization gate was applied[cite: 186, 188, 189].</p>
<p></p>

</body>
</html>