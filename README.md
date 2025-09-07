<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

    <h1>Quantum Teleportation with Qiskit</h1>

    <p>This repository contains a Jupyter Notebook that demonstrates the **Quantum Teleportation Protocol** using IBM's open-source quantum computing framework, Qiskit. It provides a detailed, step-by-step breakdown of how the protocol works, from creating an entangled pair to recovering the teleported state. The notebook uses both statevector and QASM simulators to illustrate the process and verify its success.</p>

    <p>Quantum teleportation is a process that transfers the state of a quantum particle from one location to another, without physically moving the particle itself. It is a cornerstone of quantum information science and a fundamental concept for quantum communication.</p>

    <hr>

    <h2>Directory Structure</h2>
    <pre>
    Quantum Teleportation.ipynb
    temp.txt
    README.md
    requirements.txt
    </pre>

    <hr>

    <h2>Notebook Explanation</h2>

    <h3>1. Installing Necessary Libraries</h3>
    <p>The notebook begins by installing the required Qiskit libraries using a special magic command in Jupyter notebooks. [cite_start]The <code>%pip install</code> command ensures that packages are installed directly into the notebook's Python environment, making them immediately available for use[cite: 1, 2, 3, 4].</p>
    <pre><code>%pip install qiskit qiskit-aer qiskit-ibm-provider</code></pre>
    <p>The packages installed are:</p>
    <ul>
    <li><strong><code>qiskit</code></strong>: This is the core Quantum SDK (Software Development Kit). [cite_start]It provides the fundamental tools for writing quantum circuits, simulating them, and running them on real quantum computers[cite: 5, 6].</li>
    <li><strong><code>qiskit-aer</code></strong>: A high-performance simulator that allows you to run quantum circuits on your local computer's resources. [cite_start]This is essential for testing and debugging circuits quickly without needing access to actual quantum hardware[cite: 7, 8].</li>
    <li><strong><code>qiskit-ibm-provider</code></strong>: This package is the bridge between Qiskit and IBM Quantum cloud services. [cite_start]It enables you to connect to your IBM Quantum account and execute your quantum circuits on real IBM quantum computers or their managed simulators[cite: 9, 10].</li>
    </ul>

    <hr>

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
    [cite_start]<li><code>numpy as np</code>: The standard library for **numerical computations**, used for manipulating quantum states and probabilities[cite: 14].</li>
    [cite_start]<li><code>QuantumCircuit</code>: The primary object for building a quantum program, representing a series of quantum gates and qubits[cite: 15, 16].</li>
    <li><code>QuantumRegister</code> & <code>ClassicalRegister</code>: Containers for a group of qubits and classical bits, respectively. [cite_start]Classical bits are used for storing measurement outcomes[cite: 17, 18].</li>
    [cite_start]<li><code>transpile</code>: A function that **optimizes and restructures** a quantum circuit to be compatible with a specific target hardware or simulator[cite: 19].</li>
    [cite_start]<li><code>AerSimulator</code>: The simulator from the <code>qiskit-aer</code> package, used to run the circuit locally instead of on real quantum hardware[cite: 20].</li>
    [cite_start]<li><code>plot_bloch_multivector</code>: A visualization tool to represent the state of a qubit on the **Bloch sphere**[cite: 22].</li>
    [cite_start]<li><code>random_statevector</code>: A utility to generate a random, normalized quantum state, which acts as the unknown state to be teleported[cite: 26].</li>
    [cite_start]<li><code>Initialize</code>: A special instruction used to prepare a qubit into a specific state vector[cite: 24].</li>
    [cite_start]<li><code>XGate</code> and <code>ZGate</code>: The **Pauli-X** (quantum NOT) and **Pauli-Z** (phase flip) gates, which are used to apply corrections to the teleported qubit[cite: 25].</li>
    </ul>

    <hr>

    <h3>3. Helper Functions</h3>
    <p>The notebook defines a set of helper functions that implement the core steps of the quantum teleportation protocol. [cite_start]This modular approach makes the main circuit easier to read and understand[cite: 30, 31].</p>
    <pre><code># Define helper functions
    def create_bell_pair(qc, a, b):
        """Creates a bell pair in qc using qubits a & b"""
        qc.h(a)
        qc.cx(a, b)

    def alice_gates(qc, psi, a):
        qc.cx(psi, a)
        qc.h(psi)

    def measure_and_send(qc, a, b):
        """Measures qubits a & b"""
        qc.barrier()
        qc.measure(a, 0)
        qc.measure(b, 1)

    def bob_gates(qc, qubit, crz, crx):
        """
        Applies conditional gates to the qubit
        based on the classical registers' values.
        """
        with qc.if_test((crx, 1)):
            qc.x(qubit)

        with qc.if_test((crz, 1)):
            qc.z(qubit)

    def new_bob_gates(qc, a, b, c):
        """For alternate formulation"""
        qc.cx(b, c)
        qc.cz(a, c)</code></pre>
    <ul>
    <li><strong><code>create_bell_pair(qc, a, b)</code></strong>: This function creates a maximally entangled **Bell pair** between two qubits, `a` and `b`. [cite_start]It first applies a **Hadamard (H) gate** to qubit `a` to put it into a superposition[cite: 33]. [cite_start]Then, it uses a **controlled-NOT (CX) gate** with `a` as the control and `b` as the target to entangle them[cite: 34]. [cite_start]This results in a Bell state, which is a maximally entangled state[cite: 32, 35].</li>
    <li><strong><code>alice_gates(qc, psi, a)</code></strong>: This function represents Alice's part of the protocol. [cite_start]It applies a CNOT gate with the message qubit `psi` as the control and her half of the Bell pair, `a`, as the target[cite: 37]. It then applies a Hadamard gate to `psi`. [cite_start]These gates entangle the message qubit with the Bell pair, spreading the quantum information across the system[cite: 38].</li>
    <li><strong><code>measure_and_send(qc, a, b)</code></strong>: Alice's measurement step. [cite_start]This function places a barrier and then measures her two qubits, `a` and `b`, storing the classical outcomes in two different classical registers[cite: 39]. [cite_start]These classical bits are then sent to Bob, which is the "classical communication" step of teleportation[cite: 40].</li>
    <li><strong><code>bob_gates(qc, qubit, crz, crx)</code></strong>: Bob's correction step. [cite_start]Based on the classical bits sent by Alice (`crz` and `crx`), Bob applies conditional gates to his entangled qubit[cite: 41]. [cite_start]It applies an **X gate** if `crx` is 1 and a **Z gate** if `crz` is 1[cite: 42]. [cite_start]These corrections ensure Bob's qubit becomes an exact replica of Alice's original state[cite: 43].</li>
    [cite_start]<li><strong><code>new_bob_gates(qc, a, b, c)</code></strong>: This is an alternative, more compact implementation of Bob's corrections that uses controlled gates (**CNOT** and **CZ**) instead of conditional classical logic[cite: 44, 45, 46].</li>
    </ul>

    <hr>

    <h3>4. Circuit Construction and Simulation</h3>

    <h4>4.1. Generating and Initializing the Random State</h4>
    <p>A random one-qubit quantum state, `psi`, is generated using `random_statevector(2)`. [cite_start]This state acts as the unknown quantum information that Alice wants to teleport[cite: 52]. [cite_start]The notebook then uses `array_to_latex` to display its mathematical representation and `plot_bloch_multivector` to visualize its position on a Bloch sphere[cite: 54, 56].</p>
    <pre><code># Generate random state
    psi = random_statevector(2)
    display(array_to_latex(psi.data, prefix='\\psi ='))
    plot_bloch_multivector(psi)</code></pre>
    [cite_start]<p>The state is then appended to the quantum circuit on qubit <code>qr[0]</code>, which represents Alice's message qubit[cite: 67, 68, 69]. [cite_start]A barrier is used to visually and logically separate this initialization step from the rest of the protocol[cite: 70, 71].</p>
    <p></p>

    <h4>4.2. Creating the Bell Pair</h4>
    <p>The `create_bell_pair` function is called to entangle Alice's second qubit ( `qr[1]` ) and Bob's qubit ( `qr[2]` ). [cite_start]This pair, in a maximally entangled state, serves as the quantum channel for teleportation[cite: 77, 84]. [cite_start]A barrier is added after this step to mark the completion of the entanglement setup[cite: 81].</p>
    <pre><code># Step 1: Create Bell pair
    create_bell_pair(qc, 1, 2)
    qc.barrier()
    qc.draw('mpl')</code></pre>
    <p></p>

    <h4>4.3. Alice's Entanglement and Measurement</h4>
    [cite_start]<p>Alice applies her gates using the `alice_gates` function, which entangles her message qubit ( `qr[0]` ) with her half of the Bell pair ( `qr[1]` )[cite: 86, 89]. [cite_start]A measurement is then performed on these two qubits, and the classical results are stored and conceptually "sent" to Bob[cite: 100, 105, 109].</p>
    <pre><code># Step 2: Alice's gates
    alice_gates(qc, 0, 1)
    qc.draw('mpl')</code></pre>
    <pre><code># Step 3: Measure and send
    measure_and_send(qc, 0, 1)
    qc.draw('mpl')</code></pre>
    <p></p>

    <h4>4.4. Bob's Corrections and Verification</h4>
    <p>Bob receives Alice's classical measurement outcomes. [cite_start]Based on these bits, the `bob_gates` function applies an X gate or a Z gate to his qubit ( `qr[2]` ) to perform the necessary corrections[cite: 41]. [cite_start]After these corrections, his qubit should now be in the exact state as Alice's original message qubit[cite: 43].</p>
    <p>To verify the success of the teleportation, the circuit prepares a final measurement step. [cite_start]The inverse of the original initialization gate is applied to Bob's qubit[cite: 142]. [cite_start]If teleportation was successful, this operation should revert the qubit back to its ground state, $|0\rangle$[cite: 143, 156]. [cite_start]The qubit is then measured to confirm that the outcome is indeed 0[cite: 158, 161].</p>
    <pre><code># Step 4: Bob applies gates
    bob_gates(qc, 2, crz, crx)
    qc.draw('mpl')</code></pre>
    <p></p>

    <hr>

    <h3>5. Simulation Results</h3>
    <p>The notebook runs the complete circuit on a **QASM simulator** to get realistic measurement outcomes. The simulation is run for 1024 shots to collect statistical data. [cite_start]The results are presented in a histogram and a dictionary[cite: 164, 171, 174].</p>
    <pre><code># Run on QASM simulator
    sim = AerSimulator()
    t_qc = transpile(qc, sim)
    t_qc.save_statevector()
    result = sim.run(t_qc, shots=1024).result()

    counts = result.get_counts()
    plot_histogram(counts)

    print(result.get_counts())
    {'0 0 1': 245, '0 0 0': 271, '0 1 0': 261, '0 1 1': 247}</code></pre>
    [cite_start]<p>Each key in the output represents a 3-bit string corresponding to the classical measurement outcomes from <code>cr_result</code> (Bob's final measurement), <code>crx</code> (Alice's X measurement), and <code>crz</code> (Alice's Z measurement) respectively[cite: 184, 185]. [cite_start]The fact that Bob's final measurement (the first bit) is consistently 0 across all 1024 shots confirms that the teleportation was successful, as his qubit was correctly restored to the ground state after the corrections and inverse initialization[cite: 186, 188, 189]. [cite_start]The different combinations of <code>crx</code> and <code>crz</code> in the output demonstrate the random outcomes of Alice's measurements, which were successfully used by Bob to correct his qubit[cite: 187].</p>
    <p></p>

    <hr>
</body>
</html>