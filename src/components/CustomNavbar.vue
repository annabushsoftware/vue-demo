<template>
    <b-navbar toggleable="lg" type="light" variant="info" fixed="top">
        <b-navbar-brand href="#">Vue Demo App</b-navbar-brand>
    
        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
    
        <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav>
                <b-nav-item to="/">Home</b-nav-item>
                <b-nav-item to="/about">About</b-nav-item>
                <b-nav-item to="/schedule-demo">Schedule Demo</b-nav-item>
            </b-navbar-nav>
        </b-collapse>
    </b-navbar>
</template>
